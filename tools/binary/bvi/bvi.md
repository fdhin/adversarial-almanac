---
name: "bvi"
categories:
  - blackarch-binary
version: "1.4.2"
homepage: "http://bvi.sourceforge.net/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-05-18"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "vbatts/bvi"
github-stars: 9
github-pushed: "2025-07-30"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bvi

> A vi-style editor for binary files: hex-and-ASCII window, vi keystrokes, and ex colon-commands for in-place byte editing.

## What it's for

bvi ("binary vi", by Gerhard Bürgmann) edits binary files with the same keybindings as vi — motions, searches, and ex-style colon commands — over a two-part hex/ASCII display, so vi users can patch bytes, strings, or headers without learning a point-and-click hex editor. It loads files into memory, but can window into large files by skipping to an offset (-s), stopping at an end address (-e), or loading a fixed length (-n); a read-only mode protects evidence files. The same source tree ships bmore, a bvi-style pager for binary data. It edits raw bytes only — it does not parse PE/ELF structure — and it is a curses terminal program, not a GUI.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Patch bytes in a file

```
bvi disk.img
```

Outcome: opens disk.img in the hex+ASCII window; move with vi motions, enter insert mode to overwrite bytes, then :w writes the buffer back to the file and :q quits — :sh drops to a shell without leaving bvi.

### Examine a file without modifying it

```
bvi -R evidence.bin
```

Outcome: opens the file with the read-only flag set, so write commands fail instead of saving — the safe mode for inspecting samples.

### Open a window into a large file

```
bvi -s 0x10000 -n 4096 large.img
```

Outcome: loads only 4096 bytes starting at offset 0x10000 — the skip value accepts decimal, 0x-prefixed hex, leading-zero octal, and b/k/m multiplier suffixes — keeping memory use bounded on large images; -e end loads from the start up to a fixed address instead.
