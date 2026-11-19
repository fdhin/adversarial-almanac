---
name: "sasm"
categories:
  - blackarch-misc
version: "3.16.0"
homepage: "https://github.com/Dman95/SASM"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-09-13"
build-system: make
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "Dman95/SASM"
github-stars: 5987
github-pushed: "2026-02-08"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 12
gh-activity-checked: "2026-11-09"
---

# sasm

> Cross-platform IDE for writing, running and debugging NASM, MASM, GAS and FASM assembly.

## What it's for

SASM (SimpleASM, by Dmitriy Manushin) is a Qt desktop IDE for x86 assembly written in the NASM, MASM, GAS and FASM dialects, aimed at learning and small projects: edit a program, assemble and run it in one action, and step through it with the integrated debugger (gdb-backed on Linux). It works out of the box for beginners — a fresh document assembles and runs immediately, with syntax highlighting and an output pane — and on Linux drives the system gcc/nasm/gdb toolchain underneath. It is an IDE for writing assembly, not a disassembler or reverse-engineering workbench: it does not load existing binaries for analysis (use a disassembler for that). MASM targets are Windows-oriented; NASM, GAS and FASM are the practical choices on Linux.

## Typical use cases

### Launch the IDE

```
sasm
```

Outcome: opens the SASM window with an editor pane preloaded with a sample NASM program; the run/build actions assemble and execute it in the output pane, and the debugger pane supports breakpoints, stepping and register inspection.

*No corpus evidence — derived from upstream documentation.*
