---
name: "neighbor-cache-fingerprinter"
categories:
  - blackarch-fingerprint
version: "83.f1e596f"
homepage: "https://github.com/PherricOxide/Neighbor-Cache-Fingerprinter"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-25"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "PherricOxide/Neighbor-Cache-Fingerprinter"
github-stars: 15
github-pushed: "2013-01-21"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# neighbor-cache-fingerprinter

> Remote OS detection from ARP neighbor-cache behavior — works on hosts with all TCP ports closed and ICMP filtered.

## What it's for

Neighbor Cache Fingerprinter (David Clark, PherricOxide) detects a target's
operating system by extrapolating the behavior of its ARP Neighbor Cache
instead of higher-level protocols. Traditional fingerprinters (Nmap, xprobe2)
need open TCP ports or ICMP replies; this tool elicits a reply with a single
probe (TCP SYN to an open OR closed port, UDP to a closed port, or ICMP echo),
then studies how the target's neighbor cache reacts: number and timing of ARP
request retries, cache-entry expiry, handling of the 36 gratuitous-ARP
permutations, RFC 5227 ARP-probe response correctness, and duplicate-reply
rate limiting. Because it works at layer 2, the target must be on the same
Ethernet broadcast domain as the scanner. The default scan takes about five
minutes.

## Typical use cases

### Identify the OS of a fully filtered host on the LAN

```
neighbor-fingerprint --dstip 198.51.100.10 --spoofip 198.51.100.199
```

Outcome: runs the ARP-behavior battery against the target and reports the
best-matching operating system from its fingerprint database. The `--spoofip`
address must be UNUSED on the network (several tests depend on it being absent
from the target's cache) — find a free IP with nmap or arp-scan, and change it
between runs because some targets cache it for up to 20 minutes.

### List the available probing options

```
neighbor-fingerprint --help
```

Outcome: prints the full command-line reference (probe selection, source MAC
override, timing parameters) for tailoring a scan.

*No corpus evidence — derived from upstream documentation.*
