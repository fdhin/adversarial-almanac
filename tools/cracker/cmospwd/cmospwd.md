---
name: "cmospwd"
categories:
  - blackarch-cracker
version: "5.1"
homepage: "http://www.cgsecurity.org/wiki/CmosPwd"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2019-01-26"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/CmosPwd"
github-stars: 15
github-pushed: "2019-05-04"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cmospwd

> Recovers BIOS setup passwords by decoding the CMOS memory of Award, AMI, Phoenix, and other BIOS families.

## What it's for

CmosPwd (by Christophe Grenier, cgsecurity.org) reads a computer's CMOS memory and derives BIOS setup password candidates for a long list of BIOS vendors (Award, AMI, Phoenix, IBM, Compaq, Dell, Toshiba, and more). On Linux it needs root for direct port I/O (`ioperm`); it can equally work from a previously saved CMOS dump, which is how EEPROM backups from external programmers are analysed. It also dumps raw CMOS contents and can zero the stored password as a last resort. It does not recover laptop supervisor passwords held in motherboard EEPROMs over the SMBus — those need hardware; the documentation lists per-model chip locations and dump offsets for that case.

## Typical use cases

### Recover the BIOS password of the local host
```
sudo cmospwd
```
Outcome: prints candidate passwords grouped per BIOS vendor/algorithm; use the candidates listed against the vendor shown at boot (the docs add that Award 4.50PG always accepts universal passwords such as `AWARD_SW`, and old Phoenix BIOSes accept `phoenix`). Requires root on Linux for CMOS port access.

### Dump CMOS contents
```
sudo cmospwd /d
```
Outcome: dumps the CMOS memory in ASCII and keyboard scan codes — several BIOSes store the password as scan codes rather than characters, which is exactly what this view reveals.

### Analyse a saved CMOS or EEPROM backup
```
cmospwd /d /l cmos_backup.bin
```
Outcome: decodes a backup file instead of live hardware — same per-vendor candidate list — where the dump was written earlier with `cmospwd /w cmos_backup.bin` or pulled from an external EEPROM programmer; no root port access needed.

### Clear a forgotten password
```
sudo cmospwd /k
```
Outcome: kills the CMOS password by clearing the stored value so setup opens without it; documented as a last resort and explicitly warned against on laptops, whose EEPROM-stored passwords survive CMOS clearing.

*No corpus evidence — derived from upstream documentation.*
