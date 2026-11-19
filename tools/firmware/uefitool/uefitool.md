---
name: "uefitool"
categories:
  - firmware
homepage: "https://github.com/LongSoft/UEFITool"
origin: "awesome-pentest"
recipe: "upstream"
packaged-by: [aur, nixpkgs]
build-system: cmake
github-repo: "LongSoft/UEFITool"
github-stars: 5797
github-match: "exact"
gh-checked: "2026-11-14"
github-commits-year: 120
gh-activity-checked: "2026-11-14"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-14"
license: see repository
---

# uefitool

> Cross-platform Qt GUI that parses UEFI firmware images into a tree, verifies their integrity, and lets you extract or audit every element — with CLI companions for scripting the same parse.

## What it's for

UEFITool is a C++/Qt application for tinkering with firmware images that conform to the UEFI Platform Interface (PI) specifications — BIOS ROM dumps from PCs, Intel Macs, tablets and arm64/RISC-V systems. It parses a raw image into a tree of volumes, files and sections, verifies the integrity of parsed structures (hashes, checksums) and shows everything in a GUI where elements can be inspected and their bodies extracted. The current `new_engine` line is a viewer — image reconstruction/editing is still being rebuilt, so element *replacement* only works in the legacy 0.28 `old_engine` builds (`UEFIReplace`, `UEFIPatch`). The same repository ships two non-GUI tools built on the identical engine: UEFIExtract, which dumps the parsed tree to the filesystem and emits a report plus a database of every GUID found, and UEFIFind, which searches image elements for a given pattern. It does not open encrypted vendor update formats (HP update files, Dell HDR/EXE, some InsydeFlash FD), and Intel FIT editing is not supported — dump the chip instead.

## Typical use cases

### Parse and audit a firmware image in the GUI

Open a BIOS dump and check what is inside and whether it is intact.

```
File → Open image… → select firmware.bin
```

Outcome: the image is parsed into a nested tree (firmware volumes → FFS files → sections) in the left pane; the messages pane reports any integrity violations, unknown GUIDs or parsing errors found on the way, and selecting a tree entry shows its header fields and raw body for extraction or hex inspection.

### Dump every element of an image from the CLI

Script the same parse without the GUI, keeping all tree items — not just the leaves.

```
UEFIExtract firmware.bin all
```

Outcome: the full parsed tree is dumped recursively into a `.dump` folder next to the input, one file per tree item, together with a generated report and a GUID database of the image.

### Generate a parse report or GUID inventory only

Get the structure analysis without writing a dump.

```
UEFIExtract firmware.bin report
```

Outcome: only the parsing report is produced (no dump, no GUID database) — swap the argument for `guids` to emit just the database of GUIDs present in the image, ready to diff against known-GUID lists.

*No corpus evidence — derived from upstream documentation.*
