---
technology: "Telnet"
kind: overview
audience: offensive-security
family: network-services
layer: remote-login protocol (TCP/23)
status: thin
standards: "RFC 854/855 (TELNET protocol and options)"
provenance: "evidence-grounded (5 files: 3 service-level chains plus telnet-client usage in 3 more) + upstream-verified (NVD CVE-2026-24061, RFC 854). Thin unit treated first-class per standing rule; the evidence base supplies 3 service-level chains, upstream the rest. Enrichment pass 1, 2026-11-12."
---

# Telnet

> Remote administration with no crypto at all: passwords, sessions, and every keystroke cross the wire in the clear — and in 2026 its classic GNU daemon shipped an argument-injection auth bypass straight into the CISA KEV catalog.

## What it is

TELNET (RFC 854/855) is the 1969-era remote-terminal protocol, default port **23/tcp**: a bidirectional byte stream plus in-band option negotiation (IAC sequences) for terminal parameters. Nothing in it is encrypted — the login prompt, the password, and the session are all plaintext — which is why it survives only where nobody replaced it: embedded devices, printers, industrial kit, management interfaces on old appliances, and isolated operational networks. On the attack side that obsolescence cuts two ways: the protocol is a capture prize wherever it still runs, and its daemons (BSD-derived telnetd, GNU inetutils telnetd, BusyBox telnetd on IoT, vendor stubs like HP JetDirect) are decades-stale code with fresh CVEs still landing — GNU inetutils telnetd's **CVE-2026-24061** (authentication bypass via a `-f root` value for the USER environment variable, CISA KEV as of 2026-01-26, fixed in inetutils 2.8) is the current example.

The operator takeaway: a 23/tcp banner is not trivia — it is a commitment by the target to unauthenticated-crypto-free administration, usually with vendor-default credentials, and occasionally with a daemon carrying a live KEV bypass.

## The anatomy that matters on an engagement

    client → 23/tcp → telnetd → execs login(1) (or vendor stub / BusyBox /bin/sh)
         → cleartext: banner → login: → Password: → session bytes (all sniffable)
         → IAC option negotiation (in-band, historically bug-rich parser surface)
    device stubs: HP JetDirect (password via SNMP), router BusyBox telnetd, SCADA front-ends

Three structural properties drive most findings:

- **Everything is on the wire in the clear** — a position on the segment (or any traffic-interception foothold) yields credentials, not just session data.
- **telnetd shells hand off to `login(1)`** — the daemon passes environment and arguments to the system login program; that handoff is exactly where CVE-2026-24061 lives (`USER="-f root"` → `login -f root` skips authentication), and historically where telnetd option/env injection always lived (e.g. the 2011 BSD `-froot` clan).
- **Device telnet is config-by-vendor-default** — printer and router telnet daemons ship with blank or vendor passwords and no lockout, and their credentials are frequently recoverable through an adjacent protocol (SNMP on JetDirect).

## Where you'll meet it

- **Network printers** (HP JetDirect, observed): telnet on 23 administers the printer, and the password is readable through SNMP with the default community string — the credential never needed to be strong.
- **Legacy Windows and app servers** (observed): telnet as the interactive logon path; a Windows telnet session carries the Interactive SID in its token — relevant to what the shell can touch.
- **Embedded/IoT and routers**: BusyBox telnetd, often root with no password on debug firmware; OpenWrt's dropbear replaced it years ago, so a telnet banner there means very old or deliberately enabled.
- **Industrial and isolated networks**: telnet front-ends to PLCs, RTUs, terminal servers — capture-ready and rarely patched.
- **As a client, not a service** (observed): `telnet host port` is the universal raw-TCP probe; operators use it to talk to custom services — client usage, not telnet-surface evidence, but know the idiom.

## Reading the protocol's mind

1. **`23/tcp open telnet` + version hint** gates everything; `telnet 198.51.100.10` and read the greeting — device firmware identifies itself before auth.
2. **The login/password prompts answer before any crypto could** — banner, prompts, and failures are all capture/log material on-path.
3. **GNU inetutils telnetd ≤ 2.7 answers `USER="-f root" telnet -a target` with a root shell** (CVE-2026-24061, CWE-88 argument injection into login(1)) — banner-check inetutils before assuming.
4. **JetDirect printers disclose their telnet password via SNMP** OID `.1.3.6.1.4.1.11.2.3.9.1.1.13.0` under the default community (observed; hex-decodes to the password).
5. **No lockout exists** on device stubs — credential lists run unlimited; default-cred tables (vendor: admin/blank, root/blank, admin/admin) pay before any brute.

## Where to go next

[[telnet-bug-bounty|Telnet bug bounty methodology]] — fingerprinting the daemon, capture and default-credential workflow, the SNMP-to-JetDirect chain, and the CVE-2026-24061 verify recipe.
