---
name: "pintool2"
categories:
  - blackarch-reversing
version: "5.1c1af91"
homepage: "https://github.com/sebastiendamaye/pintool2"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "sebastiendamaye/pintool2"
github-stars: 17
github-pushed: "2016-07-24"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pintool2

> Improved fork of pintool that can also recover keys in reverse order, on Intel PIN 3.x.

## What it's for

pintool2 is an improved version of wagir's pintool.py ([[pintool]]) by another author: the same instruction-count side-channel attack against key-checking binaries, with the PIN paths updated for Intel PIN 3.x toolkits. Its addition is the `-r` option, which brute-forces the key from its last character backwards — the right tool when a checker validates the tail of the input before the head, where forward recovery stalls. The shared flag surface (`-l` length, `-c` charset classes, `-b` extra characters, `-a` architecture, `-i` known prefix, `-s` filler symbol, `-d` count-delta expression, `-e` length study) is documented on [[pintool]] and works identically here. Like the original it requires an Intel PIN toolkit configured inside the script.

## Typical use cases

### Recover a key in reverse order

Start the brute force from the end of the input.

```
python3 pintool2.py -r -l 32 -c 1,2,3 -b _{$} -s - ./crackme
```

Outcome: with `-r`, hill-climbs the key backwards — the placeholder fill grows from the left, each newly fixed character printed with its instruction-count delta, until the full 32-character key is recovered. Every other flag combines as on [[pintool]], e.g. `-i 'KEY{'` to pin a known prefix while reversing the rest.

*No corpus evidence — derived from upstream documentation.*
