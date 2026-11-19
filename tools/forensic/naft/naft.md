---
name: "naft"
categories:
  - blackarch-forensic
version: "0.0.9"
homepage: "https://blog.didierstevens.com/my-software/#NAFT"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "digitalsleuth/naft"
github-stars: 5
github-pushed: "2026-06-29"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
---

# naft

> Didier Stevens' Network Appliance Forensic Toolkit: carves network frames from raw images and dissects Cisco IOS core dumps and firmware.

## What it's for

A suite of small Python programs for forensic work on network-appliance artifacts. `naft-gfe` (Generic Frame Extraction) carves Ethernet frames — ARP and checksum-valid IPv4 — out of arbitrary binary data such as memory dumps, disk images or unallocated space, and writes them to a PCAP for Wireshark. `naft-icd` (IOS Core Dumps) parses Cisco IOS core dumps: it walks memory regions and the heap linked list, dumps blocks, extracts `CW_` strings and command history, and can run YARA rules over heap blocks. `naft-ii` (IOS Image) identifies, extracts and decompresses Cisco IOS images (with optional IDA-Pro-ready patching) and batch-scans image collections against an MD5 database. One caveat on current systems: `naft-gfe` runs under Python 3, but as packaged `naft-icd` and `naft-ii` are Python-2-era scripts (they use `cStringIO` and Python-2 `exec` syntax) and need Python 2 to run.

## Typical use cases

### Carve network frames out of a memory or disk image

Reconstruct network activity from a raw dump with no filesystem involved.

```
naft-gfe carved.pcap target.img
```

Outcome: reads `target.img`, identifies ARP frames and IPv4 packets with valid checksums, and writes them to `carved.pcap`, reporting how many frames and packets were found; open the PCAP in Wireshark to analyze the recovered traffic.

### Carve frames from images larger than RAM

Process multi-GB images without loading them into memory.

```
naft-gfe -b -d carved.pcap disk.img
```

Outcome: processes `disk.img` in 100 MB blocks with 1 MB overlap (`-b`), including duplicate frames (`-d`), so oversized images are handled without exhausting memory; block and overlap sizes are tunable with `-S` and `-O`.

### Extract a compressed IOS image for analysis

Unpack a compressed Cisco IOS image for static analysis.

```
naft-ii -x ios-image.bin
```

Outcome: extracts the compressed IOS image contents; add `-i` to also patch the image so IDA Pro can load and analyze it directly.

### Batch-scan a firmware directory against known hashes

Identify a collection of IOS images quickly.

```
naft-ii -s -r -m images.csv /firmware/
```

Outcome: recursively scans `/firmware/`, identifies each image, and compares MD5 hashes against `images.csv` to flag known ones (add `-l logfile` to persist the scan results).

### Walk the heap of an IOS core dump and YARA-scan it

Inspect allocator blocks in a core dump and hunt for patterns.

```
naft-icd -y IOS_canary.yara heap core.bin
```

Outcome: parses `core.bin`'s memory structures, walks the heap linked list (the `heap` command), and checks heap blocks against the YARA rule — the bundled `IOS_canary.yara` detects stack-canary values. Other commands include `regions`, `cwstrings` and `history`.

*No corpus evidence — derived from upstream documentation.*
