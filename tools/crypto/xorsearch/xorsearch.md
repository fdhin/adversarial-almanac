---
name: "xorsearch"
categories:
  - blackarch-crypto
version: "1.11.4"
homepage: "http://blog.didierstevens.com/programs/xorsearch/"
packaged-by: [blackarch]
blackarch-builddate: "2020-07-25"
build-system: other
license: custom:public domain
meta-checked: "2026-11-09"
github-repo: "Seabreg/XORSearch"
github-stars: 6
github-pushed: "2019-05-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xorsearch

> Didier Stevens' scanner that finds a known string across every single-byte XOR, ROL, ROT, SHIFT and ADD encoding of a file.

## What it's for

XORSearch (Didier Stevens, public-domain C source) answers: "I know this file must contain `This program`, `MZ` or a C2 domain somewhere — is it hidden behind a trivial encoding?" It brute-forces every single-byte XOR key plus every ROL, ROT, SHIFT and ADD variant and reports each hit with the encoding, key, file offset and surrounding context. Use it when you know what to look for; its sibling [[xorstrings]] is the inverse tool — an untargeted extractor that dumps all strings found across those encodings. Beyond plain strings it can match hex patterns, wildcard/bit-mask rules (including a built-in library of shellcode and PE signatures), carve PE files, and write matching decoded variants to disk. It only covers these single-byte transforms — packed or multi-layer obfuscation is out of scope.

## Typical use cases

### Hunt a known string across all encodings

```
xorsearch firmware.bin "This program cannot"
```

Outcome: prints one line per hit with encoding, key, offset and context, e.g. `Found XOR 1B position 000042A0: This program cannot...` — the encoding and key in the line tell you how the blob is obfuscated. Add `-i` to ignore case, or `-h` to search for a hex byte sequence instead of ASCII.

### Restrict the encodings searched

```
xorsearch malware.bin -d 2345 "http://"
```

Outcome: disables encodings 2 (ROL), 3 (ROT), 4 (SHIFT) and 5 (ADD), leaving single-byte XOR only — faster and with fewer false positives when you already know the obfuscation family.

### Save the matching decoded variant to disk

```
xorsearch malware.bin -s "GET /"
```

Outcome: besides the hit lines, writes each matching decoded stream to `<file>.<ENC>.<KEY>` (e.g. `malware.bin.XOR.00`), ready for follow-up tools without re-implementing the transform.

### Score a sample against built-in shellcode signatures

```
xorsearch sample.bin -W
```

Outcome: matches the file across all encodings against the embedded wildcard-rule library — function prologs (`558BEC...`), GetEIP tricks, SEH setup, kernel32-base discovery, API hashing — printing each matched rule and an aggregate line like `Score: 10`. Run `xorsearch -L` alone to list all built-in rules.

### Detect embedded PE executables

```
xorsearch sample.bin -p
```

Outcome: reports PE files found under any of the encodings, with the entry-point offset and context, e.g. `Found XOR 00 position 00000000: 00000080 ........!..L.!This program cannot be r` — a quick triage for encoded loaders.

*No corpus evidence — derived from upstream documentation.*
