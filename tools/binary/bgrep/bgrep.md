---
name: "bgrep"
categories:
  - blackarch-binary
version: "27.38b746f"
homepage: "https://github.com/tmbinc/bgrep"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-03-29"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "tmbinc/bgrep"
github-stars: 427
github-pushed: "2026-06-09"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# bgrep

> grep(1) for binary files: match hex byte patterns across files and directory trees.

## What it's for

bgrep searches binary data for byte sequences given as hex strings — the job plain grep cannot do reliably. Matches are reported as `file:offset` pairs; it recurses into directories, prints leading/trailing context bytes around each hit, takes the search pattern from a file, and applies a mask file whose bytes mark don't-care positions in the pattern. With no path arguments it reads standard input, so it can sit at the end of a pipeline.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Locate a byte sequence in a file

Find where a known signature or magic value appears.

```
bgrep 424242 firmware.bin
```

Outcome: prints one `firmware.bin:<offset>` line per occurrence of the byte sequence 0x42 0x42 0x42.

### Sweep a whole directory tree

Search every file under a path at once.

```
bgrep -r 424242 ./images
```

Outcome: recursively scans `./images` and reports each matching file with the offset of every hit.

### Show context bytes around each hit

Inspect the bytes surrounding a match.

```
bgrep -C 16 4d5a dump.bin
```

Outcome: prints 16 bytes of context before and after each `MZ` (0x4d 0x5a) match; `-B` and `-A` select one-sided context instead.

### Match a pattern family with a mask file

Search for byte sequences containing wildcard positions.

```
bgrep -f pattern.bin -m mask.bin target.bin
```

Outcome: searches `target.bin` for the pattern stored in `pattern.bin`, with the mask file marking bytes that may take any value, and prints matching offsets — useful for opcode templates whose operands vary.
