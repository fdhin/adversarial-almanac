---
technology: "VNC (RFB)"
kind: overview
audience: offensive-security
family: network-services
layer: remote framebuffer protocol (5900/tcp + display N: 5901, 5902, ...)
standards: "RFC 6143 (The Remote Framebuffer Protocol, RFB 3.8)"
status: thin
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/pentesting-vnc.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "evidence-grounded (1 file under command-context gate — TightVNC registry backup 'VNC Install.reg' → stored Password hex blob decrypted with the fixed VNC DES key via Rex::Proto::RFB::Cipher). Gate note 2026-11-12: the structure pass's '10 files' collapsed to 1 under command-context gating — 'vnc' inside base64 blobs and ephemeral Ncat source ports (:59092, :59048) were collisions; other 'VNC' hits were RealVNC advertisement text in a screenshot. Thin unit treated first-class per standing rule: upstream supplies the auth model and CVE history. Upstream-verified: RFC 6143 §7.2.2 (VNC Authentication challenge-response, 8-char key truncation, 'cryptographically weak'), NVD CVE-2006-2369 (RealVNC 4.1.1 Type-1 None bypass), NVD CVE-2018-20022 (LibVNC/LibVNCServer client-side stack read). Enrichment pass 1, 2026-11-12."
---

# VNC (RFB)

> A desktop-streaming protocol whose stock authentication is a DES challenge-response keyed by a password that can only be 8 characters — and whose history includes a server that let clients simply choose "no authentication".

## What it is

VNC is remote framebuffer access: the server (TightVNC, RealVNC, TigerVNC, x11vnc, UltraVNC, and embedded forks of LibVNCServer) streams pixels from port **5900+N**, one port per display (`:1` → 5901, `:2` → 5902; a host showing many 590x ports is running many desktops, and each is a separate auth context). The wire protocol is RFB (RFC 6143): version handshake (`RFB 003.008\n` in cleartext), then a security-type negotiation — the registry defines Type 1 "None" (no auth), Type 2 "VNC Authentication" (the classic), plus TLS/VeNCrypt-family extensions (types 16-19). RFC 6143 states the security model bluntly: the protocol "provides no security beyond the optional and cryptographically weak password check" — no session encryption in the base types, so a connected session's framebuffer (and keystrokes) cross the wire in the clear.

The operator takeaway: VNC's auth is only as good as the deployment around it — a Type-1 server is instant access, a Type-2 server has at most an 8-character key you can rip from a stored config file (observed: TightVNC's registry-stored password hex-decrypted with the fixed DES key), and an on-path position sees the whole desktop stream regardless.

## The anatomy that matters on an engagement

    client → 5900+N/tcp → "RFB 003.00N" banner (pre-auth, names protocol not product)
        → security types offered → [1] None = instant desktop
                               → [2] VNC Auth: 16-byte challenge → DES(challenge, pwd[0:8])
        → session: raw framebuffer + key/pointer events, no crypto in base types

Three structural properties drive most findings:

- **The password is DES-weak by design.** RFC 6143 §7.2.2: the key is the password truncated/padded to 8 bytes; DES with a 56-bit key on a captured challenge-response pair cracks offline fast, and stored VNC passwords (registry `Password` hex, `~/.vnc/passwd`) decrypt with the *fixed* VNC key because the storage format is the same DES — an observed chain decrypted a TightVNC registry backup in one library call.
- **Security-type negotiation is client-cooperative.** CVE-2006-2369 (NVD, RealVNC 4.1.1): the server accepted a client's "Type 1 - None" selection *even when the server never offered it* — authentication bypass by answering the negotiation with the wrong byte. The class lesson: implementations that trust the client's security-type choice are broken by a one-byte patch.
- **Desktop access is session theft.** No per-session crypto in base types means an existing VNC session is visible and injectable on-path; and cloud/console variants (VNC embedded in hypervisors, IPMI KVM-over-LAN on 5900) inherit the same auth with worse passwords.

## Where you'll meet it

- **Workstations and jump hosts** (observed): TightVNC/RealVNC on admin desktops — registry/config password recovery from any file-read foothold pays directly.
- **Hypervisors and orchestration** (LibVNCServer-family builds, embedded KVM): NoVNC consoles, IPMI KVM (cross-ref [[ipmi]]) — default or blank passwords persist on appliance builds.
- **Multi-display terminal servers**: a run of 5901-590x ports is several distinct desktops (often several distinct users) — enumerate all, auth to each separately; the weakest display is the way in.
- **Client-side code** (CVE-2018-20022 class): the RFB *client* parsing a malicious server is also a surface — LibVNC/LibVNCServer before 0.9.12 shipped client-code improper initialization (stack memory read, per NVD), and viewer versions matter when you connect to hostile servers.

## Reading the protocol's mind

1. **The `RFB 003.00N` banner arrives before auth** — it fingerprints the protocol version (3.3/3.7/3.8 handshake differences) but not the product; product identity comes from behavior and the desktop name in ServerInit.
2. **Type list order tells you the security posture** — `1` in the offered types means a deployment that chose no auth; types 18/19 mean TLS/VeNCrypt (modern, harder).
3. **Max 8 password chars** — generate attack wordlists ≤8 chars; longer user passwords were silently truncated at both ends (TigerVNC's vncpasswd warns; older builds just cut).
4. **Stored passwords decrypt with a known key** — the on-disk/on-registry blob is DES with the fixed VNC key, so any read primitive (registry backup, ~/.vnc/passwd, .rdp-adjacent config dumps) equals the password in seconds.
5. **Challenge-response pairs capture** — grab the 16-byte challenge + response from the wire and crack offline; the fixed key length bounds the search.
6. **VeNCrypt (type 19) is a container, not a verdict** *(added 2026-11-13)* — its subtypes decide everything: `Plain` ships credentials without TLS, `TLSNone`/`X509None` encrypt the channel but authenticate nobody, and only `TLSVnc`/`X509Vnc`/`*Plain`-with-real-creds are both. An outer "VeNCrypt" in the negotiation is not a finding of strength — the subtype is the posture. Similarly, **5800+N** are the sibling HTTP/Java viewer ports (`5800 + display`): sweep them with the 5900s, since an auth-less web viewer sidesteps RFB authentication entirely.

## Where to go next

[[vnc-bug-bounty|VNC bug bounty methodology]] — auth-type fingerprinting, stored-password decryption, capture cracking, CVE verify recipes.
