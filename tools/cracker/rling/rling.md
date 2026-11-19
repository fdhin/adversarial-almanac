---
name: "rling"
categories:
  - cracker
version: "0~git20230629.ec27d46-0parrot1"
homepage: "https://github.com/Cynosureprime/rling"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: make
meta-checked: "2026-11-09"
github-repo: "Cynosureprime/rling"
github-stars: 102
github-pushed: "2026-09-15"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 32
gh-activity-checked: "2026-11-09"
---

# rling

> Multi-threaded, much-faster replacement for hashcat-utils' rli: wordlist dedup and line subtraction at billion-line scale.

## What it's for

High-performance line-set operations on wordlists and any line-based files: remove lines that appear in other files, remove (or keep) duplicates, intersect, sort, and run frequency analysis. It was built by the CynoSurePrime team as a from-scratch replacement for rli from hashcat-utils — on a ~10 GB, one-billion-line file it dedupes in seconds-to-minutes where rli needs tens of minutes and much more RAM. Three engines cover different constraints: the default hash-table mode, binary search (`-b`, roughly half the memory), sorted-input rli2 mode (`-2`, very low memory), and a disk-backed Berkeley DB mode (`-f`) for files larger than RAM. Gzip-compressed inputs are detected and read transparently (output is always plain text), and `stdin`/`stdout`/named pipes can be used anywhere a filename is expected. Duplicates are removed by default, always keeping the first occurrence in input order, and output is deterministic across thread counts.

## Typical use cases

### Trim a new wordlist against existing ones

Remove already-known lines (and duplicates) from a fresh dump.

```
rling new-words.txt cleaned.txt existing-dict.txt common.txt
```

Outcome: reads `new-words.txt`, drops duplicate lines, then removes every line that also appears in `existing-dict.txt` or `common.txt`; `cleaned.txt` holds the remaining unique new lines in original order. Prints an estimated memory requirement early in the run.

### Deduplicate a wordlist in place

Collapse duplicates without extra disk space.

```
rling wordlist.txt wordlist.txt
```

Outcome: the whole input is read into memory before the output file is opened, then `wordlist.txt` is rewritten with one copy of each line — first occurrence kept, original order preserved.

### Keep only lines shared with other files

Intersect instead of subtract.

```
rling -c all-names.txt matches.txt known-a.txt known-b.txt
```

Outcome: `matches.txt` receives only the lines from `all-names.txt` that also appear in at least one of the other files; if nothing matches, no data is written.

### Process a file bigger than RAM

Fall back to disk-backed mode when memory runs out.

```
rling -f -M 4g giant.txt trimmed.txt stale.txt
```

Outcome: uses a Berkeley DB on disk with a 4 GB cache instead of holding everything in memory, so arbitrarily large inputs work at the cost of speed; the temporary databases are created in the current directory (or the `-T` path).

### Measure wordlist contents

Frequency analysis instead of set operations.

```
rling -q h wordlist.txt stdout
```

Outcome: prints a line-length histogram (count, percent, cumulative) of `wordlist.txt` to stdout — e.g. which password lengths dominate; `-q cw` lists the most common lines with counts, and any extra file arguments report how many input lines also appear in those files.

*No corpus evidence — derived from upstream documentation.*
