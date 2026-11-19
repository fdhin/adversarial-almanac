---
technology: "VNC (RFB)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "evidence-grounded (1 file: TightVNC 'VNC Install.reg' registry backup, Password hex 6BCF2A4B6E5ACA0F-style blob decrypted via Metasploit's Rex::Proto::RFB::Cipher.decrypt to the VNC password; the structure pass's 10-file count was base64/port collisions — see [[vnc|overview]] gate note). Thin unit: upstream supplies the rest — RFC 6143 auth handshake, NVD CVE-2006-2369, NVD CVE-2018-20022. Companion overview: [[vnc|VNC]]. Enrichment pass 1, 2026-11-12."
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/pentesting-vnc.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# VNC bug bounty methodology

Target model: 5900+N/tcp (each display N its own auth), RFB 3.3/3.7/3.8. Server families:
TightVNC, RealVNC, TigerVNC, x11vnc, UltraVNC, LibVNCServer-based appliances. Companion
overview: [[vnc|VNC]].

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Type 1 (None) offered or accepted | security-type list contains 1 / CVE-2006-2369 build | **yes** | Instant desktop — Critical |
| Stored password recovery | file read reaching VNC config/registry | file read | Plaintext-equivalent password — High |
| Challenge-response crack | on-path capture of challenge+response | capture | Password (≤8 chars) offline — High |
| Default/weak credentials | appliance/managed deployments | credentialed | Desktop session — High |
| Unencrypted session interference | on-path, base security types | capture | View + inject keystrokes — High |
| CVE-2018-20022-class client bugs | outdated viewer connecting to hostile server | n/a (client) | Stack disclosure/ASLR defeat to RCE chains |

## Phase 0 — Fingerprint auth posture

```bash
nmap -sV -p 5900-5920 198.51.100.10     # which displays exist; "VNC protocol 3.8" + product hints
telnet 198.51.100.10 5900               # banner: "RFB 003.008" (pre-auth)
# read the offered security types: nmap vnc-info NSE or a raw RFB client prints them
nmap --script vnc-info,vnc-title -p 5900 198.51.100.10   # desktop title leaks the account/desktop
```

Types offered: `[1]` = no auth (stop, you are done — connect with any viewer); `[2]` =
classic VNC auth (continue below); `[16,17]` = Tight/Ultra auth; `[18,19]` = TLS/VeNCrypt
(modern; password attacks move to the credential plane). The vnc-title NSE reads the
desktop name post-negotiation on permissive servers — free target intelligence.

Automated none-auth companions (added 2026-11-13, rewritten from HackTricks):
nmap's `realvnc-auth-bypass` NSE tests the CVE-2006-2369 negotiation flaw
from the scan itself, and Metasploit's
`auxiliary/scanner/vnc/vnc_none_auth` reports whether a server accepts the
`None` security type — chain either behind `vnc-info`:

```bash
nmap -sV --script vnc-info,realvnc-auth-bypass,vnc-title -p 5900 198.51.100.10
msf> use auxiliary/scanner/vnc/vnc_none_auth
```

### Reading the negotiation properly: force one scheme at a time (added 2026-11-13)

Do not stop at the product banner or the outer type list. For RFB 3.7+ the
server sends the security-type numbers and the client picks — so *make the
client pick, one scheme per connection*, and read what actually negotiates.
TigerVNC's vncviewer is the instrument (flags verified against its man
page): `-SecurityTypes=` accepts `None`, `VncAuth`, `Plain`, `TLSNone`,
`TLSVnc`, `TLSPlain`, `X509None`, `X509Vnc`, `X509Plain` (plus the
RA2 family); `-ViewOnly` suppresses your keyboard/pointer events, `-Shared`
asks the server to keep existing connections instead of kicking them — the
low-impact validation pair. Verbose negotiation lands on stderr:

```bash
# confirm an advertised no-authentication path WITHOUT sending input:
vncviewer -Log '*:stderr:100' -Shared -ViewOnly -SecurityTypes=None 198.51.100.10::5900
# classic VNC auth on its own:
vncviewer -Log '*:stderr:100' -Shared -ViewOnly -SecurityTypes=VncAuth 198.51.100.10::5900
# walk the TLS/X.509 container subtypes separately:
vncviewer -Log '*:stderr:100' -Shared -ViewOnly -SecurityTypes=TLSNone,TLSVnc,TLSPlain,X509None,X509Vnc,X509Plain 198.51.100.10::5900
```

The subtype reading is the point: `VeNCrypt (19)` outer is not enough to
call the service authenticated *or* encrypted — `Plain` carries credentials
in the clear with no TLS, `TLSNone`/`X509None` encrypt the channel but
perform no client authentication, and the certificate-validation result
matters as much as the cipher. Record the selected subtype and cert verdict
from the verbose output, not the banner. Also sweep **5800-5801** while you
are there: the HTTP/Java viewer siblings can expose the same desktop with
their own (often weaker) gate.

## Phase 1 — CVE-2006-2369 verify recipe (RealVNC 4.1.x Type-1 bypass)

NVD: RealVNC 4.1.1 (and products embedding it — AdderLink IP, Cisco CallManager era)
accepts a client-selected security Type 1 "None" **even when not offered**. Verify:
connect raw, read `RFB 003.008`, send `0x01` as the security-type choice; if the server
proceeds to SecurityResult/init instead of closing, it is vulnerable — capture the
transcript, do not hijack production sessions. Fixed in 4.1.2+; the pattern (client
picks an unoffered type) is worth re-testing on embedded forks that never took the patch.

## Phase 2 — Stored password decryption (observed chain)

VNC servers persist the DES-encrypted password; the storage uses the fixed VNC key, so
recovery = instant plaintext:

```text
Windows (TightVNC/RealVNC): HKCU\Software\TightVNC\Server / ORL\RealVNC "Password"
  → hex blob (e.g. 16 hex chars) in a .reg backup, config share, or backup exec dump
Linux: ~/.vnc/passwd (8 obfuscated bytes)
```

Observed: an SMB-accessible user folder held `VNC Install.reg` (registry export);
its `Password` value `6BCF2A4B6E5ACA0F` decrypted with one call — Metasploit's
`Rex::Proto::RFB::Cipher.decrypt ["6BCF2A4B6E5ACA0F"].pack('H*'), key` (the tooling
exists in metasploit-framework; the fixed key is public in RFB implementations). Then
`vncviewer 198.51.100.10:0` with the password.

Password-file locations and tooling (added 2026-11-13, rewritten from
HackTricks; paths verified against TigerVNC's vncviewer(1)): check the
legacy path **and** the XDG migration — current TigerVNC writes
`$XDG_CONFIG_HOME/tigervnc/passwd`, falling back to
`~/.config/tigervnc/passwd`, while older servers keep `~/.vnc/passwd`. The
standalone decryptor **vncpwd** (jeroennijhof) recovers the plaintext from
the legacy fixed-key DES format (`vncpwd <passwd-file>`; the Windows build
also enumerates stored VNC passwords in the registry and accepts raw or hex
blobs) — the no-Metasploit alternative to the Rex cipher call.

## Phase 3 — Weak/default credentials and multi-display sweep

No lockout on most VNC daemons; per-display auth is independent. Sweep every open 590x
port with a viewer-driven or hydra-style list (`hydra -P vnc-passes.txt vnc://198.51.100.10:5901`).
Defaults seen in the wild: appliance docs' shipped passwords, blank on debug builds,
`password`/`vnc`/vendor names. Cross-protocol: hypervisor/IPMI consoles often expose the
same desktop via VNC on 5900 with the BMC's credentials (see [[ipmi]]).

## Phase 4 — On-path: crack the challenge, then the session

Capture a handshake: server sends 16-byte challenge, client returns DES(challenge,
pwd-key). RFC 6143 bounds the key to 8 password bytes — crack the pair offline
(hashtooling for DES-RFB, or metasploit's vnc_password capture modes). With base
security types the session itself is unencrypted: an on-path attacker watches the
framebuffer stream and injects pointer/keyboard events — treat any VNC over untrusted
transit as shared, and prefer finding the TLS-typed servers' creds instead.

## Phase 5 — Client-side verification (CVE-2018-20022 class)

NVD: LibVNC/LibVNCServer before 0.9.12 (commit 2f5b2ad) — CWE-665 improper
initialization in **client** code reads stack memory (info disclosure; ASLR-bypass
partner for other bugs). Engagement angle: targets running stale vncviewer/LibVNC-based
tools can be fed a hostile "VNC server"; check installed versions before connecting your
own tooling to unknown servers, and flag stale embedded viewers on the target.

## Phase 6 — Wrapper front-ends and the real bind boundary (added 2026-11-13)

Modern deployments wrap VNC in an authenticated front-end (web gateways,
per-user proxies, notebook/desktop services) — the attack is to skip the
gate and talk RFB to the backend directly. The pattern is real, not
theoretical: **CVE-2025-32428 / GHSA-vrq4-9hc3-cgp7** (jupyter-remote-desktop-proxy,
fixed 3.0.1; record verified via the CVE/GHSA entries) — the proxy was
designed around per-user Unix sockets from 3.0.0 on, but its TigerVNC
backend stayed reachable over the network (TurboVNC deployments were not
affected). Front-end authentication that the backend never enforces is the
finding.

Two checks, from two positions:

```bash
# on the host (any foothold): what did the wrapper ACTUALLY bind?
ss -ltnp '( sport >= :5800 and sport <= :5999 )'
# from the segment that should NOT have direct VNC access:
nmap -Pn -sV -p 5800-5999 198.51.100.10
```

Treat a wrapper's "socket-only" or "authenticated proxy" claim as a
hypothesis: loopback-only, wildcard, container, and host-network listeners
have very different reachability, and container/bridge networking routinely
re-exposes what the compose file thought was private. Document the boundary
you actually connected through — backend RFB with no front-end auth is the
evidence.

## Command quick reference

```bash
nmap -sV --script vnc-info,vnc-title -p 5900-5910 TARGET
vncviewer TARGET:0 -SecurityTypes VncAuth    # force the auth type explicitly (avoid None surprises)
hydra -P wordlist vnc://TARGET:5900
```

## Tool mapping (prose)

nmap vnc-info/vnc-title scripts for posture and titles; vncviewer (TigerVNC/RealVNC
clients) for session access; hydra for credential attacks; Metasploit's
vnc_login/vnc_password tooling and Rex RFB cipher for stored-password decryption
(observed in Phase 2); Wireshark for handshake capture. Tools vault owns per-tool pages.
