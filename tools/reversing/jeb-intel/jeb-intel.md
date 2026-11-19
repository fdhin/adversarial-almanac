---
name: "jeb-intel"
categories:
  - blackarch-reversing
version: "3.7.0.201909272058"
homepage: "https://www.pnfsoftware.com/jeb/intel"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-16"
build-system: scripts
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# jeb-intel

> JEB decompiler, Intel x86 variant — same PNF Software product as [[jeb-android]].

## What it's for

This package is the same commercial JEB reverse-engineering platform by PNF Software, delivered as a demo build licensed for Intel native code: its GENDEC decompiler pipeline handles x86 32-bit and 64-bit code (x87, MMX, SSE and further extensions with varying support). The BlackArch package layout is identical to the other JEB variants — a `/usr/bin/jeb-intel` launcher that runs `/opt/jeb-intel/jeb_linux.sh` with all arguments forwarded — so the launcher, console-mode flags (`-c --script=...`, `-c --license`), GUI workflow and licensing behavior are all documented once in the canonical note, [[jeb-android]]. Use cases are not duplicated here.

*No corpus evidence — derived from upstream documentation.*
