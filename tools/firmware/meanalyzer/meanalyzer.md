---
name: "meanalyzer"
categories:
  - blackarch-firmware
version: "1.273.0"
homepage: "https://github.com/platomav/MEAnalyzer"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "platomav/MEAnalyzer"
github-stars: 1348
github-pushed: "2026-09-06"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# meanalyzer

> Identify, validate, and unpack Intel Management Engine (CSME/CS-TXE/SPS), Graphics System Controller, and related independent firmware from BIOS dumps or standalone update files.

*No corpus evidence — derived from upstream documentation.*

## What it's for

ME Analyzer parses Intel Engine firmware families — ME 2–15, TXE 0–4, SPS 1–5 and GSC 100 — plus the CSE-independent PMC, PCHC, PHY and OROM firmware, and reports Family, Version, Release, Type, Date, SKU, Platform, Size and Health Status for each image. It ships with community-maintained databases (`MEA.dat`, `Huffman.dat`, `FileTable.dat`) that let it recognize known firmware and flag new, unknown or problematic versions against the public Engine/Graphics firmware repositories — useful both for auditing exactly what a board runs and for firmware research. Beyond reporting it can fully unpack CSE/GSC code and file-system structures (FPT, BPDT/IFWI, FTBL/EFST, VFS). It handles Intel engine firmware only, not generic UEFI volumes (use uefi-firmware-parser for those). Runs anywhere with Python 3.7 or newer; on BlackArch the `meanalyzer` wrapper executes `MEA.py` directly.

## Typical use cases

### Identify a single firmware image non-interactively

```
meanalyzer firmware.bin -skip -exit
```

Outcome: prints the firmware's Family, Version, Release, Type, Date, SKU, Platform, Size and Health Status, plus notes/warnings if the image is new, unknown or problematic. Without `-skip` the tool first shows its interactive welcome/options screen, and without `-exit` it pauses with a press-enter prompt — both flags belong in any scripted invocation.

### Batch-scan a directory of firmware files

```
meanalyzer firmware_collection/ -mass -skip -exit
```

Outcome: analyzes every file in the given directory one by one, reporting the same details per image — a quick way to inventory engine firmware versions across a collection of dumps or extracted BIOS updates.

### Show partition and layout tables

```
meanalyzer firmware.bin -skip -dfpt
```

Outcome: additionally prints the Flash Partition Table (FPT), Boot Partition Descriptor Table (BPDT/IFWI), OROM and CSE/GSC layout information, showing how the engine region is organized before any deeper unpacking.

### Unpack CSE/GSC/IUP firmware structures

```
meanalyzer firmware.bin -skip -unp86 -out unpacked/
```

Outcome: fully unpacks all supported CSE, GSC and independent (IUP) firmware partitions and file systems into the output directory for inspection of the engine's code and data files. Add `-ver86` for verbose unpacking output or `-bug86` to pause on errors during unpacking.

### Export machine-readable reports

```
meanalyzer firmware.bin -skip -json
```

Outcome: writes a parsable JSON info file describing the firmware; `-html` produces the same information as HTML. Combined with `-mass`, this gives a scripted pipeline a structured record of every image scanned.
