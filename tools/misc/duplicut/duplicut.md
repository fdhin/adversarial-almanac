---
name: "duplicut"
categories:
  - blackarch-misc
version: "2.2"
homepage: "https://github.com/nil0x42/duplicut"
packaged-by: [blackarch, aur]
blackarch-builddate: "2020-12-06"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nil0x42/duplicut"
github-stars: 983
github-pushed: "2026-11-06"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 18
gh-activity-checked: "2026-11-09"
---

# duplicut

> Order-preserving deduplicator for massive password wordlists — handles files bigger than RAM without sorting.

## What it's for

duplicut (nil0x42) removes duplicate lines from huge wordlists without sorting them, which matters for dictionary-based password cracking: when you concatenate multiple sources, the most probable passwords must stay at the front so they crack first, and `sort -u` destroys that ordering. It is written for size — a memory-mapped, chunked, compressed-hashmap design lets it dedupe wordlists larger than available RAM, multithreaded, with a progress bar and ETA. Beyond plain dedupe it can fold case (`--lowercase`/`--uppercase`) so case variants collapse, keep only printable-ASCII lines, cap line length, and save the removed duplicates for inspection.

## Typical use cases

### Dedupe a merged wordlist, keeping the order

Clean the concatenation of several source lists without reordering it.

```
duplicut wordlist.txt > clean-wordlist.txt
```

Outcome: writes the deduplicated list to stdout with the original line order preserved — redirect (or use `-o`) to capture it; a progress bar with ETA tracks the scan, which streams through files too large to fit in memory.

### Fold case while deduplicating

Collapse `Password`, `password` and `PASSWORD` into one entry.

```
duplicut -c -o clean.txt wordlist.txt
```

Outcome: lowercases every line (`-c`; `-C` uppercases instead), treats the folded forms as duplicates, and writes the normalized, deduplicated result to clean.txt.

### Clean an oversized list for a cracking run

Apply the line-hygiene filters in the same pass.

```
duplicut -p -l 64 -o clean.txt massive.txt -D dupes.txt
```

Outcome: drops non-printable-ASCII lines (`-p`) and lines longer than 64 bytes (`-l`, maximum 4095), dedupes the remainder into clean.txt, and records the removed duplicate lines in dupes.txt — producing a john/hashcat-ready list within a known line-size budget.

*No corpus evidence — derived from upstream documentation.*
