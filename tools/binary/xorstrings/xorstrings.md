---
name: "xorstrings"
categories:
  - binary
version: "0.0.1"
homepage: "https://didierstevens.com/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: other
license: custom
meta-checked: "2026-11-09"
github-repo: "DidierStevens/DidierStevensSuite"
github-stars: 2576
github-pushed: "2026-10-13"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 16
gh-activity-checked: "2026-11-09"
---

# xorstrings

> Didier Stevens' scanner for strings hidden behind single-byte XOR, ROL or SHIFT encoding.

## What it's for

XORStrings (Didier Stevens, 2013, public-domain C source) is the `strings` counterpart for trivially obfuscated data: it brute-forces every single-byte XOR key, ROL rotation and SHIFT variant of a file and reports the encoded strings it finds, revealing text that plain `strings` misses — useful for a first pass over malware droppers and configuration blobs. With a known operation and key (`-o`/`-k`) it decodes just that variant instead of scanning, and `-s` writes the transformed files to disk for follow-up tools. It handles only these simple encodings, not packed or multi-layer obfuscation.

*No corpus evidence — derived from upstream documentation.*

GitHub: the author's suite repository DidierStevens/DidierStevensSuite carries the compiled xorstrings binaries (Windows `xorstrings.exe`, `OSX/xorstrings`); the C source (XORStrings.c) is distributed only via didierstevens.com.

## Typical use cases

### Scan a binary for encoded strings

```
xorstrings sample.bin
```

Outcome: prints candidate XOR/ROL/SHIFT-encoded strings found across all keys and rotations (minimum length 5), sorted by string count per encoding — a fast obfuscated-string triage.

### Decode with a known operation and key

```
xorstrings -o XOR -k 0x5a sample.bin
```

Outcome: skips brute-forcing and prints the strings obtained by XOR-ing the file with key 0x5a; `-o` accepts exactly `XOR`, `ROL` or `SHIFT`, and `-k` takes decimal or 0x-prefixed hex.

### Tune the scan and export CSV

```
xorstrings -l 8 -c sample.bin > hits.csv
```

Outcome: raises the minimum string length to 8 and writes findings as CSV for scripted triage; `-m` switches sorting to maximum string length and `-t` changes the terminator byte.

### Write decoded variants to disk

```
xorstrings -s sample.bin
```

Outcome: additionally saves each transformed copy as `sample.bin.<OPERATION>.<KEY>` (e.g. `sample.bin.XOR.5A`), so the decodable variants can be re-analyzed with any other tool.
