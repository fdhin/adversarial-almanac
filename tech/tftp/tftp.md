---
technology: "TFTP"
kind: overview
audience: offensive-security
family: network-services
layer: "trivial file transfer (UDP/69, lock-step, no authentication, no directory listing)"
standards: "RFC 1350 (TFTP rev 2, STD 33); option extensions RFC 2347/2348/2349 (blksize/tsize); servers: tftpd-hpa, atftpd, SolarWinds Free tftpd (Windows)"
homepage: https://www.rfc-editor.org/rfc/rfc1350
status: thin
provenance: "evidence: 2 files under the command-context gate (2026-11-12; markers tftp/69-udp, tftp client verb lines, tftpd banners) — one full chain (SolarWinds Free tftpd on Windows XP: absolute-path reads, binary-mode PUTs of an EXE and a MOF into system32/wbem\\mof, WMI auto-compile -> SYSTEM) and one sighting (69/udp open|filtered tftp only — detection context, chain went via SNMP). Upstream-verified: RFC 1350 (no auth, no listing, 512-byte blocks, netascii/octet/mail modes, error codes); nmap tftp-enum NSE docs + nmap's actual nselib/data/tftplist.txt (255 entries: Cisco phone loads, Polycom configs, admin-confg/backup-config/boot-config, plus a ..\\..\\..\\boot.ini traversal probe; A.B.C.X-confg generated per-target); tftpd-hpa tftpd(8) (--create, --read-only, --max-upload/RFC 2349 tsize, --secure chroot); atftpd(8) (--daemon/--bind-address). Enrichment pass 1, wave 4."
---

# TFTP

> A file server with no authentication, no listing, and no encryption — where "attacker reads a file" is the protocol working as designed, and "attacker writes a file" depends entirely on which directory the daemon was pointed at.

## What it is

TFTP (Trivial File Transfer Protocol, RFC 1350, 1992, STD 33) is UDP disk-boot engineering from the BOOTP/PXE era: a client sends a Read Request or Write Request (`RRQ`/`WRQ`: opcode, filename, mode) to UDP/69, the server answers from a new source port, and the file moves in fixed 512-byte blocks, each acknowledged before the next — a lock-step designed to fit in ROM. Three transfer modes exist (`netascii`, `octet`, `mail`), and the option extensions (RFC 2347/2348/2349) negotiate `blksize`, timeout and `tsize`. The RFC's own words frame the entire attack surface: *"It cannot list directories, and currently has no provisions for user authentication."*

No auth means every property that matters is daemon configuration. **Read scope**: classic Unix daemons chroot to a serving directory (tftpd-hpa `-s/--secure`), but Windows freeware daemons have historically served absolute paths — one observed box ran SolarWinds Free tftpd and happily executed `get /windows/system32/eula.txt`. **Write scope**: tftpd-hpa by default *only allows upload of files that already exist*, and refuses new files unless started with `-c/--create` (which creates them world-read-writable unless `--umask`/`--permissive` say otherwise); `--ro` kills uploads entirely, `--max-upload` caps them via the RFC 2349 tsize option; the Windows freeware generation had no such fence, which is how an EXE and a WMI MOF landed in `system32` and `system32\wbem\mof`. **Enumeration**: because there is no listing, discovery is dictionary-based with an error oracle — error 1 "File not found" vs a stream of DATA packets — which is exactly what nmap's tftp-enum script automates, using a 255-entry built-in list (Cisco IP-phone firmware loads, Polycom `{mac}.cfg`/`directory.xml` provisioning files, `admin-confg`/`backup-config`/`boot-config` network-device configs, even a `..\..\..\..\..\..\..\boot.ini` traversal probe) plus generated per-target Cisco names `A.B.C.X-confg` for X in 0..255.

The operator takeaway: an answering UDP/69 is a **pre-auth read primitive whose reach is defined by the daemon's chroot discipline, and a write primitive wherever uploads are enabled**. The payloads that live on TFTP servers are unusually valuable per byte — device configs with credentials, phone provisioning files, firmware images — and the write side converts directly into code execution in the contexts that consume TFTP: PXE boot files, firmware staging for printers/ICS, and the Windows WMI MOF trick the evidence base demonstrates end-to-end.

## The anatomy that matters on an engagement

    client → UDP/69: RRQ <filename> octet            (no handshake, no auth, ever)
      ← DATA block 1 (512 B) from new src port → ACK 1 → ... → DATA <512 B = EOF
      ← ERROR 1 "File not found"                     (the dictionary oracle)
    client → UDP/69: WRQ <filename> octet
      ← ACK 0 → DATA...                              (only if daemon allows writes)
    enum = dictionary × error oracle;  write = WRQ against daemon policy

Three structural properties drive most findings:

- **Filename is the entire API.** Absolute paths, UNC-ish device paths and `..` traversal all reduce to "what does this daemon resolve that name to" — the chroot (or absence of one) is the boundary, and the nmap filelist itself carries a traversal probe.
- **The error oracle makes enumeration reliable.** File-not-found (error 1) vs access-violation (error 2) vs data — three distinguishable answers turn any name list into a filesystem probe.
- **Consumers make writes lethal.** PXE/tftp boot dirs, phone/printer firmware roots and `wbem\mof` on Windows auto-execute what lands in them; a WRQ is file upload, but the *context* is code execution.

## Where you'll meet it

- **Network device config archives** — routers/switches that `copy run tftp:` on a schedule; `A.B.C.X-confg`/`admin-confg`/`backup-config` reads yield device credentials (see also [[snmp]] for the same estate).
- **VoIP provisioning roots** — the tftp-enum list is dominated by it: Cisco `P003-*`/`OS79XX.TXT` loads, Polycom `{mac}.cfg`; configs here carry SIP registrar credentials.
- **Printers and ICS/firmware staging** — devices that pull firmware and config over TFTP at boot; a write-enabled root is firmware substitution (persistent device compromise).
- **PXE/boot environments** — deployment networks serving boot images; read = image theft (often embedded creds), write = boot-time code execution.
- **Windows hosts with freeware tftpd** — the SolarWinds-freeware generation: whole-filesystem read via absolute paths, and the MOF-into-`wbem\mof` write chain to SYSTEM (WMI compiles it automatically).

## Reading the daemon's mind

1. **UDP/69 is the initial contact only** — data flows from a fresh server port to a fresh client port; firewalls that only allow 69 break mid-transfer, and "open|filtered" in a UDP scan (as in the observed sighting) needs a real RRQ to resolve.
2. **512-byte lock-step is the speed governor** — big files crawl (the observed EXE took ~117s for 250 KB); plan payload sizes and patience accordingly, and expect `blksize` negotiation (RFC 2348) only on modern clients/servers.
3. **Mode matters on Windows**: request `octet`/binary for anything that is not text — a netascii GET of a binary corrupts it silently.
4. **Write behavior fingerprints the daemon**: WRQ → ERROR 2 (access violation) is tftpd-hpa default (existing-files-only or read-only); ACK 0 to a new filename means `--create` (or freeware) — the write primitive exists.
5. **TFTP is also an exfil/pivot transport**: it is UDP, tiny, and everywhere on OT/print segments — a staged file out via WRQ to an attacker-controlled tftpd (or a device config pulled through a compromised host) blends into legitimate provisioning traffic. See the [[tftp-bug-bounty|methodology]] for the commands.

## Where to go next

[[tftp-bug-bounty|TFTP bug bounty methodology]] — UDP detection, tftp-enum dictionary runs, read/write probing with the error oracle, and the config/firmware/MOF weaponization chains.
