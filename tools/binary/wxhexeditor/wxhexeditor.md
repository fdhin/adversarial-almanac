---
name: "wxhexeditor"
categories:
  - blackarch-binary
version: "v0.24.r109.gf439d8f"
homepage: "http://wxhexeditor.sourceforge.net/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-02-02"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "EUA/wxHexEditor"
github-stars: 620
github-pushed: "2024-06-27"
github-archived: false
github-issues: 83
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wxhexeditor

> wxWidgets GUI hex editor for very large files, raw disks and devices.

## What it's for

wxHexEditor is a free cross-platform (Linux, Windows, macOS) hex and disk editor whose strength is handling very large files and raw block devices without loading them fully into memory. It offers hex/text search and replace, byte-range selection, file tags and annotations, checksums (via mhash) and a built-in disassembler view (via udis86), plus a comparison mode that highlights differences between two files. Reach for it when a GUI is the right instrument — carving a disk image, patching a binary at a known offset, diffing two dumps. It is an interactive application: the command line only opens files and sets initial view state, it does not script edits.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Open a file at a specific offset

```
wxhexeditor --offset 4096 image.bin
```

Outcome: launches the GUI with image.bin loaded and the caret positioned at byte offset 4096 (`-o` is the short form).

### Open a file with a byte range preselected

```
wxhexeditor --select 512:1023 image.bin
```

Outcome: launches the GUI with bytes 512–1023 already selected; `--select` also accepts a single `<byte offset>` or `<start>+<length>` forms.

### Compare two files

```
wxhexeditor --compare dump1.bin dump2.bin
```

Outcome: opens the GUI in comparison view highlighting bytes that differ between the two files; `--compare` takes exactly two filenames and cannot be combined with `--offset` or `--select`.
