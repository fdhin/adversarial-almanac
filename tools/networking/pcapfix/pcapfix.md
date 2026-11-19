---
name: "pcapfix"
categories:
  - blackarch-networking
version: "1.1.7"
homepage: "http://f00l.de/pcapfix/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2021-07-14"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Rup0rt/pcapfix"
github-stars: 233
github-pushed: "2026-03-13"
github-archived: false
github-issues: 6
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# pcapfix

> Repairs corrupted or truncated pcap and pcapng files so analyzers can open them again.

## What it's for

pcapfix repairs damaged packet captures instead of discarding them: it rebuilds an intact pcap global header (or prepends a synthesized one when it is missing entirely), then locates and repairs corrupted per-packet headers. For pcapng it reconstructs missing Section Header and Interface Description Blocks, fixes invalid block sizes, and repairs or skips broken option fields. Use it when Wireshark or tcpdump refuse a truncated or crashed capture; it always writes the repaired copy to a new file and leaves the original untouched. It cannot recover packet payload bytes that were never written to disk.

## Typical use cases

### Repair a corrupted pcap

Fix a capture with a damaged header so tools open it.

```
pcapfix broken.pcap
```

Outcome: scans the global header and packet headers, repairs what is broken, and writes the result to `fixed_broken.pcap`; the console reports each corruption found and a final count fixed.

### Deep-scan a heavily damaged capture

Force packet detection beyond the default scan window.

```
pcapfix -d -v mangled.pcap
```

Outcome: `-d` extends the search for the first packet beyond the first 256 KiB (needed when the start of the file was heavily destroyed), and `-v` prints verbose per-packet repair detail; output lands in `fixed_mangled.pcap`.

### Repair a pcapng file

Rebuild block structure in a next-generation capture.

```
pcapfix -n damaged.pcapng
```

Outcome: forces pcapng handling — missing Section Header/Interface Description Blocks are recreated and invalid block sizes or option fields repaired — writing `fixed_damaged.pcapng`.

### Rebuild a capture whose link type is known

Set the data link type explicitly when the original header is gone.

```
pcapfix -t 113 -o rebuilt.pcap headerless.dump
```

Outcome: synthesizes a global header with data link type 113 (Linux cooked capture) instead of the default 1 (Ethernet) and writes the repaired file as `rebuilt.pcap`; pass the DLT number matching how the packets were originally captured.

*No corpus evidence — derived from upstream documentation.*
