---
name: "densityscout"
categories:
  - blackarch-misc
version: "45"
homepage: "https://www.cert.at/en/downloads/software/software-densityscout"
packaged-by: [blackarch]
blackarch-builddate: "2021-07-30"
build-system: scripts
license: ISC
meta-checked: "2026-11-09"
github-repo: "foreni-packages/densityscout"
github-stars: 3
github-pushed: "2015-09-27"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# densityscout

> Ranks every file under a path by byte density so packed, encrypted — and therefore suspicious — binaries float to the top of the list.

## What it's for

DensityScout (by Christian Wojner, CERT.at) computes a per-file "density" — an entropy-like byte-distribution metric — for every file in a file-system path and prints them in descending order. Packed and run-time-encrypted executables, the typical signature of obfuscated malware, score far higher density than ordinary programs or text, so unknown malware concentrates at the top of the list. It is statistical triage, not a scanner: there are no signatures and no verdicts, only a ranked ordering of how uniform each file's bytes look. Mode ABS is the default (a typical packed file lands near 0.9); mode CHI squares the distances and is meant for files larger than ~100 KB (packed ≈ 1000.0).

## Typical use cases

### Rank a directory tree by density

```
densityscout -r /usr/bin
```

Outcome: walks the path recursively and prints every file in descending density order; the first entries are the most packed or encrypted files found and the natural starting point for closer inspection.

### Triage specific file types above a threshold

```
densityscout -r -g 0.85 -s exe,dll /opt/software
```

Outcome: only files with suffix exe or dll whose ABS density exceeds 0.85 (`-g`) are printed — a compact candidate list instead of a full ranking.

### Filter Windows executables by magic number, capped to the worst hits

```
densityscout -r -pe -n 30 C:\Windows\System32
```

Outcome: includes only files identified as portable executables by magic number (`-pe`, catching renamed binaries that no longer match `-s` suffixes) and prints at most 30 lines (`-n`).

### Use CHI mode for large files

```
densityscout -r -m CHI -pe C:\Windows\System32
```

Outcome: densities are computed in chi-square mode, where a typical packed file scores around 1000.0 — the recommended mode when scanning files larger than about 100 KB.

*No corpus evidence — derived from upstream documentation.*
