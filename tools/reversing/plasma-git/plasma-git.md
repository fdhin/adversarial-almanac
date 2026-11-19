---
name: "plasma-git"
categories:
  - reversing
version: "20190304.r922"
homepage: "https://github.com/plasma-disassembler/plasma"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "plasma-disassembler/plasma"
github-stars: 3069
github-pushed: "2021-08-31"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# plasma-git

> Interactive disassembler for x86/ARM/MIPS that renders indented, colored pseudo-code next to the disassembly.

## What it's for

Plasma (the `-git` package tracks the repository's master; the project was originally named "Reverse") is an interactive disassembler by joelpx. It loads ELF, PE and raw files for x86/x64, ARM and MIPS (the latter two partially), builds a navigable function database, and generates readable indented pseudo-code with colored syntax — a readability layer over the disassembly, not a standalone decompiler emitting C. The flow-graph view can be flipped by inverting conditional jumps, and the whole UI runs in a terminal (use `--nocolor` where 256-color support is missing). It is scriptable through an embedded Python API and ships helper scripts in `plasma/scripts`. Development largely stopped in 2021 and database compatibility between versions is not guaranteed — pin a version for long projects.

## Typical use cases

### Open a binary and read pseudo-code

Get from a raw binary to readable function views.

```
plasma -i ./target.bin
```

At the `>>` prompt:

```
v main
```

Outcome: loads the file into Plasma's database and jumps to `main`; pressing Tab over a function toggles its pseudo-decompilation and `|` splits the window to show disassembly and pseudo-code side by side.

### Run the bundled analysis scripts

Use the Python API without writing your own.

```
plasma -i ./target.bin
```

At the prompt:

```
py !strings.py
py !xrefsto.py main
py !crypto.py
```

Outcome: executes the scripts shipped in `plasma/scripts` through the embedded interpreter — string listing, an xdot call-graph of references to a function, and detection of known crypto constants. With Keystone installed, `py !asm.py CODE` and `py !disasm.py HEX_STRING` assemble and disassemble arbitrary snippets in-session.

*No corpus evidence — derived from upstream documentation.*
