---
name: "ssdp-scanner"
categories:
  - blackarch-scanner
version: "1.0"
homepage: "http://packetstormsecurity.com/files/127994/SSDP-Amplification-Scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BlackArch/blackarch"
github-stars: 3563
github-pushed: "2026-11-08"
github-archived: false
github-issues: 35
github-match: "search"
gh-checked: "2026-11-09"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1007
gh-activity-checked: "2026-11-09"
---

# ssdp-scanner

> Scapy-based SSDP sweep: sends M-SEARCH discovery packets across an IP range and logs every UDP/1900 responder with its reply size.

## What it's for

This is a small Python 2/Scapy script (anonymous author, distributed via Packet Storm as an SSDP amplification scanner; BlackArch installs it as `ssdp-scanner`). It sends SSDP `M-SEARCH ssdp:discover` packets across an address range and records every host that answers on UDP/1900 together with its reply size — identifying UPnP/SSDP-capable devices and, in amplification assessments, which responders would work as reflectors and how large their replies are. The raw output list is meant to be post-processed (the author's comments include awk one-liners for ranking by reply size and packet count). It is a single-purpose sweep script: no port, delay or protocol options, and no resume. The anonymous Packet Storm release has no author repository; the packaged script is carried verbatim (sha512-identical to the PKGBUILD digest) as `packages/ssdp-scanner/ssdp-amplifier.txt` inside the BlackArch package collection — this note's GitHub link and stats describe that collection, not an author home.

## Typical use cases

### Sweep a range for SSDP responders

```
ssdp-scanner 198.51.100.1 198.51.100.254 scannedlist.txt
```

Outcome: writes one line per responding host (IP and reply size in bytes) to `scannedlist.txt` as replies arrive — every listed host answered an SSDP M-SEARCH on the multicast discovery port.

### Rank the reflectors by reply size

```
cat scannedlist.txt | awk '$2 > 300 {print $1}' | sort -u
```

Outcome: filters the sweep output to hosts whose replies exceeded 300 bytes and prints the unique IPs — the strongest reflectors, ranked; the threshold pattern comes from the author's own sorting suggestions in the script comments.

*No corpus evidence — derived from upstream documentation (packaged script source).*
