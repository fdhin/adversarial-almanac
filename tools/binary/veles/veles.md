---
name: "veles"
categories:
  - blackarch-binary
version: "2018.05"
homepage: "https://github.com/codilime/veles"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-02-05"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "codilime/veles"
github-stars: 1239
github-pushed: "2019-06-16"
github-archived: true
github-issues: 56
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# veles

> Visual binary-data explorer that renders file chunks as digram/trigram images and statistics (archived upstream).

## What it's for

Veles (CodiLime, archived since 2019) is a Qt GUI application for exploring large binary files: it chunks the data, computes per-chunk statistics and entropy, and renders them as digram and trigram visualizations so that structure — headers, embedded files, encoded regions — becomes visible at a glance, backed by built-in chunk parsers including Kaitai Struct formats. Reach for it when a file is too large to scroll through in a hex editor and the first task is locating interesting regions. It is a visual analysis aid, not a disassembler or decompiler. Development stopped in 2019; the archived GitHub repository is the only live source (the former project sites are gone).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Open a binary file for visual analysis

```
veles firmware.bin
```

Outcome: launches the GUI with firmware.bin loaded as a chunk visualization; pan and zoom the rendered data image to spot structured or high-entropy regions, then drill into chunk detail views.

### Open several related files side by side

```
veles dump1.bin dump2.bin
```

Outcome: opens each positional argument in the maximized main window, letting you compare structure across related images or dumps.

### Check the CLI options the GUI accepts

```
veles --help
```

Outcome: prints the Qt command-line help (help and version options plus positional file arguments), confirming what can be scripted before the GUI takes over.
