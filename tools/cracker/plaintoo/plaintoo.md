---
name: "plaintoo"
gentoo-category: "app-crypt"
version: "0.1"
homepage: "http://www.plain-text.info/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# plaintoo

> PyGTK front-end for dumping Windows password hashes with bkhive and samdump2, both bundled.

## What it's for

plaintoo is a mid-2000s graphical (PyGTK) wrapper around two classic offline hash-dumping steps: it takes a Windows `SYSTEM` hive and `SAM` hive and drives the bundled `bkhive` (which recovers the SYSKEY bootkey from the SYSTEM hive) and `samdump2` (which uses that bootkey to dump the password hashes stored in the SAM) behind one point-and-click window instead of two manual commands. The package installs a `plaintoo` launcher that simply starts the GUI plus command-line copies of `bkhive` and `samdump2` alongside it. It has no command-line interface of its own — for anything scripted, use `bkhive` and `samdump2` directly (each has its own note in this vault). The original project site is long dead; the tool survives only in the Pentoo overlay.

*No corpus evidence — derived from upstream documentation.*
