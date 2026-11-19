---
name: "rbasefind"
categories:
  - blackarch-binary
version: "41.a661118"
homepage: "https://github.com/sgayou/rbasefind"
packaged-by: [blackarch]
blackarch-builddate: "2021-05-19"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "sgayou/rbasefind"
github-stars: 375
github-pushed: "2020-09-27"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rbasefind

> Brute-force load-address (base address) finder for flat 32-bit firmware images.

## What it's for

Embedded firmware dumps are often header-less flat binary blobs, and loading
them at the wrong base address makes disassembly and pointer references
useless. rbasefind scans such an image for ASCII strings and for 32-bit words
that could be pointers, then ranks candidate base addresses by the greatest
intersection between interpreted pointer values and string offsets. It is a
Rust reimplementation of the well-known basefind.py/basefind.cpp approach,
deliberately using as little prior knowledge about the file as possible. The
heuristic works well on some ARM (non-Thumb) firmware and is not guaranteed —
treat the output as a ranked shortlist to confirm in the disassembler.

## Typical use cases

### Find the base address of a firmware dump

```
rbasefind firmware.bin
```

Outcome: prints the number of strings and pointer-like words located, then a
ranked list of candidate base addresses with intersection counts (e.g.
`0x00002000: 2195`); the top-scoring address is typically the correct load
address to configure in the disassembler.

### Handle big-endian images

```
rbasefind firmware.bin -b
```

Outcome: pointer words are interpreted as big-endian before the intersection
scan, producing the same ranked candidate list for big-endian (e.g. some
MIPS/ARM) firmwares.

### Speed up scans of very large images

```
rbasefind fw_all.bin -m 100
```

Outcome: raises the minimum string length from the default 10 to 100, which
shrinks the string set drastically — upstream reports a multi-minute scan
dropping to a fraction of a second at some cost in ranking sharpness. Useful
as a first cheap pass before running the default scan.

*No corpus evidence — derived from upstream documentation.*
