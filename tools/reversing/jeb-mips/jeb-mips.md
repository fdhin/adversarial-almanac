---
name: "jeb-mips"
categories:
  - blackarch-reversing
version: "5.20.0.202411121942"
homepage: "https://www.pnfsoftware.com/jeb/mips"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-04"
build-system: scripts
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# jeb-mips

> JEB decompiler, MIPS variant — same PNF Software product as [[jeb-android]].

## What it's for

This package is the same commercial JEB reverse-engineering platform by PNF Software, delivered as a demo build licensed for MIPS native code: its GENDEC decompiler pipeline handles 32-bit and 64-bit MIPS binaries, the ISA typical of embedded routers and IoT firmware. The BlackArch package layout is identical to the other JEB variants — a `/usr/bin/jeb-mips` launcher that runs `/opt/jeb-mips/jeb_linux.sh` with all arguments forwarded — so the launcher, console-mode flags (`-c --script=...`, `-c --license`), GUI workflow and licensing behavior are all documented once in the canonical note, [[jeb-android]]. Use cases are not duplicated here.

*No corpus evidence — derived from upstream documentation.*
