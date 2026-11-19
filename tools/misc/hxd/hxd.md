---
name: "hxd"
categories:
  - blackarch-misc
version: "2.5.0.0"
homepage: "https://mh-nexus.de/en/hxd/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2023-02-28"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# hxd

> Freeware Windows hex and disk editor, packaged for Linux to run under Wine.

*No corpus evidence — derived from upstream documentation.*

## What it's for

HxD (by Maël Hörz, mh-nexus.de) is a freeware closed-source Windows hex editor and disk editor: fast editing of files of any size, raw disk and RAM access, search/replace including wildcard byte patterns, checksums, a file-compare mode for diffing two files or disks, and unlimited undo. The Linux package wraps the official installer's binaries in a Wine launcher, so what runs is the Windows GUI on an emulation layer — there is no native CLI, no scripting surface and no headless mode (for those, use native tools such as ImHex or xxd). The packaged launcher starts the editor and does not forward command-line arguments, so everything after launch happens in the GUI.

## Typical use cases

### Launch the editor under Wine

Start the GUI from a Linux shell.

```
hxd
```

Outcome: opens the HxD window (HxD64.exe under Wine). From there, opening files or a raw disk, searching, comparing and editing are all menu-driven; the launcher accepts no arguments, so files are opened with File → Open rather than from the command line.
