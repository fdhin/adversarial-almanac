---
technology: "SIP (Session Initiation Protocol)"
kind: overview
audience: offensive-security
family: network-services
status: "thin (2 evidence files pass the re-gated command-context gate, both fingerprint-tier — upstream carries the methodology)"
layer: VoIP signaling protocol (UDP/TCP 5060, TLS 5061), RFC 3261
language: "text protocol (SIP messages); PBX implementations in C"
homepage: "https://datatracker.ietf.org/doc/html/rfc3261"
provenance: "evidence-grounded THIN (2 files pass the re-gated command-context gate: SIP/2.0, 5060/tcp|udp, sip: URIs, sipvicious/svmap/svwar/svcrack/sipcrack, Asterisk PBX command-context; exemplars: a UDP scan flags Asterisk PBX on its default port — fingerprint-tier; an nmap SIPOptions probe answered SIP/2.0 200 OK — fingerprint-tier; NO file demonstrates a full SIP attack chain, so this unit is upstream-led: RFC 3261 (signaling/proxy/registration model), EnableSecurity SIPVicious OSS README (svmap scanner / svwar extension enumeration / svcrack digest-auth cracking, sipvicious_* console scripts), SIPVicious wiki SVCrack usage (-u/-d/-r/-z/-D, nonce reuse), nmap NSE sip-methods (Allow-header OPTIONS probe), 2026-11-12. Structure-pass hint was 5 on 'sipvicious'/'5060' — re-gate excluded nmap probe-class name-drops and regex-quantifier 'asterisk' prose."
---

# SIP

> A PBX on the network answers a text protocol where extension numbers are usernames and passwords guard voicemail-toll fraud: scan for servers, enumerate extensions, crack the digest — the whole audit is three tools deep.

## What it is

SIP is the signaling protocol of IP telephony (RFC 3261): it sets up, modifies and tears down sessions while the media (voice) flows over RTP on other ports. It is a text protocol cut from the same cloth as HTTP — methods (`OPTIONS`, `REGISTER`, `INVITE`, `BYE`, `SUBSCRIBE`, ...), headers, response codes (`SIP/2.0 200 OK`) — carried over **UDP or TCP 5060** (TLS variant sips on 5061). A **PBX** (Asterisk being the open-source archetype) answers `REGISTER` from endpoints (each with an extension number and a password), routes `INVITE`s between them, and bridges to the PSTN.

The operator's mental model: a user directory that authenticates with **HTTP-digest-style challenges** (`WWW-Authenticate` with nonce/realm), where the "usernames" (extensions) are short digit strings and the passwords are routinely weak or default. Registration is what an attacker wants: a registered extension can make calls — toll fraud, voicemail access, and social-engineering callbacks from the company's own number.

This surface is deliberately **fingerprint-first** (a UDP scan flagging Asterisk; a port answering an nmap SIP probe with `SIP/2.0 200 OK`). No observed attack chain exists for this surface, so everything past fingerprinting here is upstream-derived from the SIPVicious toolset and RFC 3261 — the standard three-step VoIP audit: **scan (svmap) → enumerate extensions (svwar) → crack digest auth (svcrack)**.

## The anatomy that matters on an engagement

    UDP/TCP 5060 (sips 5061)
      → OPTIONS sip:... → response Allow: header = the method surface (nmap sip-methods)
      → REGISTER sip:extension@realm → 401 + WWW-Authenticate (nonce, realm)
           → extension exists?   401 vs 404          ← extension enumeration (svwar)
           → password correct?   200 OK vs 401/403   ← digest crack (svcrack)
      → INVITE sip:extension@... → calls out through the PBX (toll fraud, fraud-alerting: DESTRUCTIVE)
      → PBX management side channels: Asterisk manager interface, SSH on the appliance

Three structural properties drive most findings:

- **404-vs-401 is an oracle.** The registrar's answers separate "extension exists" from "no such user" — that is how extension enumeration works without touching a single password (svwar's design, upstream).
- **Digest auth with short numeric usernames.** Extensions are 3-5 digit numbers; numeric-range cracking (`-r 1000-9999`) and default-password scans (`-D`) are the documented svcrack modes — PBX audit is largely a numbers game.
- **UDP makes scanning noisy but cheap.** A stateless text protocol invites ranged scanning (svmap over CIDRs); defenders who watch see it immediately — rate-limit when stealth matters.

## Where you'll meet it

- **Corporate phone systems** — Asterisk/FreePBX appliances, Cisco/Avaya desk-phone estates; the PBX sits on the voice VLAN, often reachable from the user VLAN.
- **UC/contact-center engagements** — extension counts in the thousands (call centers), giving the 404/401 oracle real yield; voicemail PINs ride the same directory.
- **Cloud/hosted PBX** — SIP over TLS 5061 to the provider; the digest attack moves to the registrar hostname, not the port scan.
- **Adjacent surface** — the same appliance runs web admin panels (FreePBX), Asterisk's manager interface, and SSH: extension access is the sanctioned entry, but the box itself is a Linux target once credentialed elsewhere.

## Reading the server's mind

1. **An `Allow:` header answering `OPTIONS`** is the method map — `INVITE, ACK, CANCEL, OPTIONS, BYE, REFER, SUBSCRIBE, NOTIFY, INFO` (nmap sip-methods output shape, upstream) — a PBX answering everything is a full registrar+proxy.
2. **`SIP/2.0 200 OK` from a plain port probe** means an RFC-3261 speaker is live even when the banner says nothing (observed via nmap probe).
3. **UDP 5060 open on an Asterisk** (observed) implies REGISTER/INVITE handling plus AMI on the loopback of the same appliance — enumerate the host, not just the port.
4. **Realm strings leak identity** (`Digest realm="asterisk"` or company-branded realms) — free PBX fingerprinting from a single 401.
5. **Extension ranges are directory-shaped**: reception (0/100), departments (x100 blocks), conference rooms — enumerate systematically, not randomly.

## Where to go next

[[sip-bug-bounty]] — the phased agent methodology: discovery scanning, extension enumeration, digest cracking, and the boundaries (what to stop at) when the evidence base offers no worked examples.
