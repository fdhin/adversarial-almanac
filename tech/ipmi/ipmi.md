---
technology: "IPMI / BMC"
kind: overview
audience: offensive-security
family: hardware
layer: out-of-band management protocol (ASF-RMCP/RMCP+ over UDP 623, plus per-vendor HTTP/SSH/VNC surfaces)
language: "C (vendor firmware)"
first-released: 1998
license: "Intel-developed specification; implementations are proprietary firmware"
homepage: "https://www.intel.com/content/www/us/en/products/docs/servers/ipmi/ipmi-technical-resources.html"
github-repo: ""
evidence: "1 evidence file (command-context gate: \\bIPMI\\b/asf-rmcp words and port 623 in service lines — the parent hint of 24 files was almost entirely '623' digit noise: byte counts, hash substrings, file sizes; only one file attacks IPMI)"
status: thin
provenance: "field-mined from 1 engagement (see internal coverage ledger) + upstream-weighted per thin-unit policy: Rapid7 'A Penetration Tester's Guide to IPMI and BMCs' (HD Moore, 2013, Dan Farmer research), hashcat.net example_hashes (mode 7300), OpenBMC/ipmitool usage references, accessed 2026-11-12"
---

# IPMI / BMC

> A management processor that, by specification, will hand you the salted password hash of any account you name — before you authenticate — and whose "cipher 0" mode authenticates any password at all; access to it is functionally physical access to the server.

## What it is

IPMI (Intelligent Platform Management Interface, Intel-led specification, versions 1.5 and 2.0, first published 1998) is the protocol spoken by Baseboard Management Controllers — embedded ARM computers (HP iLO, Dell iDRAC/DRAC, IBM IMM, Fujitsu iRMC, Supermicro IPMI, Oracle/Sun ILOM, ASUS iKVM) soldered onto server motherboards. The BMC runs its own Linux, keeps running when the host is off, and can monitor, reboot, reinstall, and KVM the host. IPMI 1.5 speaks RMCP over UDP 623; IPMI 2.0 adds RMCP+ (`lanplus`) with the RAKP session protocol — and vendors habitually expose a whole second surface next to it: web management (80/443), SSH or telnet CLI, VNC (5900), WS-MAN (5985), even UPnP (49152).

The operator's mental model: a slow, weird, spec-frozen embedded device whose authentication protocol was designed in the 1990s and cannot be patched into safety — the two headline flaws (cipher 0, RAKP hash disclosure) are *in the specification*, so the only real remediation is network isolation.

The takeaway: BMCs respond to a single UDP packet, sit at the very bottom of the network (forgotten VLANs, "lights-out" segments nobody scans), and yield either outright authentication bypass (cipher 0) or an offline-crackable hash for every account (RAKP). Default credentials finish the job on anything unmanaged. On the one observed IPMI box, the cracked BMC password was then reused on the application running on the host — the standard pivot.

## The anatomy that matters on an engagement

    attacker
      → UDP 623 (ASF-RMCP ping / RMCP+ session)
        ├─ IPMI 1.5: cipher 0 absent, MD5 auth
        ├─ IPMI 2.0 RAKP: BMC returns salted HMAC of the user's password pre-auth → offline crack (hashcat -m 7300)
        └─ cipher 0 (spec flaw, HP/Dell/Supermicro): any password accepted → user list / user creation
      → vendor surface (22 SSH, 80/443 lighttpd, 5900 VNC, 5985 WS-MAN, 49152 UPnP)
      → host control: chassis power, SOL console, virtual media = effective physical access

Three structural properties drive most findings:

1. **RAKP hash leak is unconditional and unpatchable.** "The authentication process for IPMI 2.0 mandates that the server send a salted SHA1 or MD5 hash of the requested user's password to the client, prior to the client authenticating" (Dan Farmer, via Rapid7). You name a user; the BMC mails you the crackable verifier.
2. **Cipher 0 authenticates anything.** Cipher suite 0 = no integrity/confidentiality; HP, Dell, and Supermicro BMCs accepted session-open with any password (Rapid7). With any valid username (they leak via RAKP anyway), `-C 0` turns "auth" into a formality — and `user set name/password/priv/enable` plants a fresh ADMINISTRATOR.
3. **Defaults are vendor constants.** Supermicro `ADMIN/ADMIN`, Dell iDRAC `root/calvin`, IBM `USERID/PASSW0RD`, Oracle ILOM `root/changeme`, Fujitsu/ASUS `admin/admin` — only HP iLO randomizes at factory. BMCs that were never rotated are one try each.

## Where you'll meet it

- **Dedicated lights-out VLANs and lab/server segments** — UDP 623 scans (or the Metasploit `ipmi_version` module across a /24) light up BMCs that have never seen a security test; slow devices, so throttle and wait.
- **One server in scope** — the BMC is often out of the client's mental model of "what's on this box": a second, softer system attached to the same motherboard with its own creds (frequently reused from the host's app accounts, per the observed pivot).
- **Cloud/co-lo metal** — customer BMCs on shared management networks; RAKP leaks cross-tenant verifiers if segments are flat.
- **After the hash crack** — passwords chosen for BMCs are human (rockyou-class); the observed chain cracked the Administrator hash with rockyou in one pass, then reused it on the app.

## Reading the framework's mind

- A one-packet UDP probe answers with the IPMI version, OEM id, supported auth types, and cipher suites — recon without ever opening a session (msf `ipmi_version`; nmap `ipmi-version`NSE-style scripts exist too).
- `ipmitool -I lanplus` is the IPMI 2.0 client; `-C 0` selects cipher suite 0. `user list` shows the account table (ID, name, flags, channel privilege); `channel getaccess <ch> <uid>` is the per-user detail view — these two are the post-auth recon pair.
- RAKP dumps crack as hashcat mode **7300** (`IPMI2 RAKP HMAC-SHA1`); the dumped line is `<user>:<salted-hash>` — Metasploit's `ipmi_dumphashes` formats it ready for `hashcat -m 7300 hash rockyou.txt` (observed, exact chain).
- BMC auxiliary services have their own default creds and bugs (lighttpd web UIs, Dropbear SSH, VNC without auth on some Supermicro firmware) — enumerate 623 *and* the TCP range around it.
- Dell iDRAC exposes `delloem` extensions in ipmitool; Supermicro IPMI 2.0 boxes frequently also answer plain `ADMIN/ADMIN` on their web UI — the credential is shared across surfaces.

## Where to go next

[[ipmi-bug-bounty|IPMI/BMC bug-bounty methodology]] — phased: discovery (UDP 623) → version/cipher recon → RAKP hash dump + offline crack → cipher 0 bypass + backdoor user (DESTRUCTIVE) → vendor surfaces and default-credential matrix → disclosure notes.
