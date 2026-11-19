---
name: "fred"
categories:
  - blackarch-windows
version: "0.1.1"
homepage: "https://www.pinguin.lu/fred"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-11-16"
build-system: windows
license: GPL3
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# fred

> Cross-platform graphical editor for offline Windows registry hive files.

## What it's for

FRED, from pinguin.lu, is a cross-platform Microsoft registry hive editor: it opens Windows registry hives (SYSTEM, SOFTWARE, SAM, SECURITY, NTUSER.DAT, ...) as plain offline files and lets you browse and edit their keys and values directly — no running Windows system required and none of the live-hive locking that regedit imposes. It is built on the hivex library with a Qt interface (Qt4 with QtWebKit in the packaged 0.x releases), so it handles hives pulled from a dead machine, a disk image, or a mounted forensic copy. Typical uses are offline repairs (fixing a broken service configuration, editing stored settings before first boot), forensic inspection of a hive's contents, and preparing images in bulk. It is a purely interactive GUI application with no command-line interface, and it edits hive files on disk rather than a live registry.

*No corpus evidence — derived from upstream documentation.*
