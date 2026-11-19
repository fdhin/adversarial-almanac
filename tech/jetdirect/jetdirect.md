---
technology: "JetDirect / raw network printing"
kind: overview
audience: offensive-security
family: network-services
status: "thin (3 evidence files pass the re-gated command-context gate — upstream carries the methodology)"
layer: printer network services (9100/tcp raw, 23/tcp telnet admin, SNMP 161/udp config)
language: "device firmware; PJL/PCL/PostScript page languages"
homepage: "https://www.hp.com/us-en/printers/"
provenance: "field-grounded THIN (3 evidence files pass the re-gated command-context gate: 9100/tcp, JetDirect, LaserJet, @PJL/PJL, HP OID 1.3.6.1.4.1.11, printer+SNMP combos; chains: SNMP gdPasswords OID -> telnet -> JetDirect console; PRET over raw 9100, PJL device/job enumeration, queued-job decryption; @PJL payload via CUPS FoomaticRIP — cross-ref [[cups]]; structure-pass hint was 9 on bare 'jetdirect'/'9100' markers — re-gate killed 'Pretty Raw Hex' Burp-OCR panels and JSON_PRETTY_PRINT constants) + upstream-verified: RUB-NDS PRET README (PS/PJL/PCL modes, fs commands), HP-LASERJET-COMMON-MIB gdPasswords = .1.3.6.1.4.1.11.2.3.9.1.1.13 (JetDirect print-server MIB, OctetString r/w), exploit-db 22319 / CVE-2002-1048 (SNMP JetAdmin device-password disclosure, snmputil get ... 1.3.6.1.4.1.11.2.3.9.1.1.13.0), 2026-11-12."
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/9100-pjl.md), CC BY-NC 4.0, accessed 2026-11-13"
  - "rewritten from HackTricks (src/network-services-pentesting/515-pentesting-line-printer-daemon-lpd.md), CC BY-NC 4.0, accessed 2026-11-13"
  - "rewritten from HackTricks (src/network-services-pentesting/pentesting-631-internet-printing-protocol-ipp.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# JetDirect / raw network printing

> A network appliance that answers three protocols with no authentication story to speak of — raw 9100 executes whatever page language you speak, telnet guards a password SNMP will read back to you, and the job queue keeps copies of everything the office prints.

## What it is

"JetDirect" is HP's print-server stack (external boxes and embedded cards) and the shorthand for the standard printer network surface: **9100/tcp raw printing** (connect, send bytes, the printer interprets them), **23/tcp telnet** for the admin console, and **SNMP 161/udp** with the `public` community for configuration — including the HP enterprise MIB tree (`.1.3.6.1.4.1.11...`) where the telnet password itself lives (`gdPasswords` at `.1.3.6.1.4.1.11.2.3.9.1.1.13`, readable since the 2002 disclosure era — CVE-2002-1048 / exploit-db 22319).

The page languages are the attack surface: a printer receiving bytes on 9100 will interpret **PCL**, **PJL** (the job-control layer above PCL) and **PostScript** as spoken. PJL and PostScript are Turing-complete-ish file-system citizens: they can list directories, read and write files on the device, enumerate device configuration and queued jobs. The reference tool is PRET (RUB-NDS) which wraps all three languages behind a UNIX-like shell (`ls`, `cat`, `get`, `info`...).

The operator takeaway is that a printer is a **logged-in network citizen with stored documents**: print queues retain job copies, configuration pages and memory hold network settings (SMTP, LDAP, WiFi credentials), and the device often holds a domain or LDAP service account for scan-to-email/scan-to-folder — compromising it pivots from the printer to the credentials it uses.

Three chains dominate this surface: one demonstrates the SNMP-password-to-telnet path, another the PRET/PJL enumeration over raw 9100, and a third delivers an `@PJL` payload through CUPS (that chain belongs to [[cups]]; the printer side is here).

## The anatomy that matters on an engagement

    port scan → 9100/tcp (raw print) | 23/tcp (telnet) | 161/udp (SNMP, community 'public')
      → SNMP: snmpwalk the HP tree → gdPasswords OID → telnet console password (CVE-2002-1048 class)
      → telnet 23: JetDirect console → config, network settings, credential stores
      → 9100: speak the device's language (PostScript? PJL? PCL?)
           PRET: ./pret.py 198.51.100.10 ps|pjl|pcl
             → info (device, RAM/ROM, encryption support)
             → ls/cat/get: printer file system, queued jobs (document disclosure)
             → env: stored network credentials (SMTP/LDAP/WiFi)
      → pivot: scan-to-email/scan-to-folder service accounts

Three structural properties drive most findings:

- **No handshake on 9100.** There is no banner and no auth step — the first bytes you send select the language; PRET's three-mode probe (`ps` fails → try `pjl`) is the protocol negotiation (observed).
- **The admin password is a readable SNMP object.** On old HP firmware the telnet password can be walked straight out of the MIB (observed; upstream: gdPasswords OID, 2002 disclosure) — telnet consoles were never a boundary if you could SNMP-read.
- **Queues and file systems persist documents.** `get` on a queued job returns the printed document — sometimes encrypted with a key the device's own `info` command describes (observed: an AES-CBC job pulled along with the config that says so).

## Where you'll meet it

- **Office network interiors** — printers are on the user VLAN, scanned rarely, patched never; 9100/23/161 open simultaneously is the fingerprint.
- **Print-management engagements** — document confidentiality is the model: queued jobs, stored copies, fax buffers.
- **Credential pivot points** — scan-to-email SMTP logins, LDAP bind accounts for address books, WiFi PSKs on network config pages; these are real accounts elsewhere in the estate (see [[snmp]] for the general SNMP surface, [[telnet]] for telnet).
- **Adjacent print subsystems** — CUPS servers (`cups` unit) and Windows spoolers talk *to* these devices; one observed chain injects a PJL payload into a CUPS-rendered job (cross-ref [[cups]]).

## Reading the device's mind

1. **9100 open with no banner** = raw printing; the first bytes decide the language — probe PostScript, then PJL, then PCL (PRET does exactly this; observed: the ps attempt failed, PJL connected and named the device `LaserCorp LaserJet 4ML`).
2. **SNMP `public` answering the HP tree** means config — and on old firmware the telnet password — is one `snmpwalk` away.
3. **The PJL `info` block** (RAM/ROM, supported encryption) tells you what queued-job exfil will look like — AES/CBC support means encrypted blobs with keys potentially retrievable from config.
4. **Telnet 23 answering "HP JetDirect"** identifies the admin console and its password prompt — the prompt the SNMP OID fills.
5. **A CUPS/print server in front** means the printer also receives rendered jobs from the network — injection may be possible without touching 9100 directly (an observed pattern).
6. **515 and 631 are the same device's other doors** *(added 2026-11-13)* — an open **515/tcp** is LPD, whose data files reach the same PostScript/PJL interpreters as raw 9100 (a print job is a language-delivery vehicle); an open **631/tcp** is device-side IPP, with pre-auth parser-RCE history on major vendors (Lexmark CVE-2023-50739, Canon CVE-2023-0856). Fingerprint all three before choosing a lane — and remember 9100 itself can carry *new* job languages: the Canon ImageCLASS XPS/TrueType-hinting VM chain reached root-level code execution from `@PJL ENTER LANGUAGE = XPS` alone (Phase 6d of the methodology).

## Where to go next

[[jetdirect-bug-bounty]] — the phased agent methodology: SNMP to password chain, telnet console, PRET language probing, file system and queue disclosure, credential harvesting, and hardening notes.
