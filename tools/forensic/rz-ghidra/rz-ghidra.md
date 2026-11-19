---
name: "rz-ghidra"
categories:
  - forensics
  - hardware
  - reverse-engineering
version: "0.8.0-1"
homepage: "https://github.com/rizinorg/rz-ghidra"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali]
arch-builddate: "2025-06-15"
build-system: cmake
license: LGPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "rizinorg/rz-ghidra"
github-stars: 1006
github-pushed: "2026-10-31"
github-archived: false
github-issues: 63
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 22
gh-activity-checked: "2026-11-09"
---

# rz-ghidra

> Ghidra's decompiler and SLEIGH disassembler inside rizin, via the `pdg` command family.

## What it's for

rz-ghidra integrates the decompiler half of NSA's Ghidra (self-contained C++,
Ghidra itself not required) and the SLEIGH disassembler into [[rizin]] — the
fork of radare2 (see also [[radare2]]). It adds the `pdg` command family to any
rizin session, so functions can be decompiled to C-like pseudocode, as JSON, or
as annotations on the disassembly, and SLEIGH language handling can be
inspected or overridden. The plugin must match the installed rizin version —
releases are tagged with the compatible rizin version (e.g. `rz-0.x.y`).

## Typical use cases

### Decompile a function to pseudocode

Read a binary's logic as C-like source.

```
rizin -qc 'aaa; s main; pdg' ./firmware.bin
```

Outcome: analyzes the binary, seeks `main` and prints its Ghidra-decompiled
pseudocode to stdout.

### Get decompilation as JSON

Feed decompiled output to scripts and diffing tools.

```
rizin -qc 'aaa; s main; pdgj' ./firmware.bin
```

Outcome: dumps the current function's decompilation as structured JSON
(positions, tokens, statements) instead of plain text.

### Show pseudocode side by side with offsets

Map decompiled lines back to addresses.

```
rizin -qc 'aaa; s main; pdgo' ./firmware.bin
```

Outcome: prints the decompiled function with instruction offsets interleaved,
so each pseudocode line ties to its binary location.

### Annotate the disassembly with pseudocode

Persist decompiler output inside the rizin database.

```
rizin -qc 'aaa; s main; pdg*' ./firmware.bin
```

Outcome: writes the decompiled lines back as comments at their addresses;
afterwards `pdf` in a session shows pseudocode alongside the disassembly.

### List the loaded SLEIGH languages

Check which architectures the plugin can describe.

```
rizin -qc 'pdgs' ./firmware.bin
```

Outcome: prints every loaded SLEIGH language definition, confirming which
architecture IDs are available to force when auto-detection fails.

### Force a specific SLEIGH language

Override architecture auto-detection before decompiling.

```
rizin -qc 'e ghidra.lang=x86:LE:32:default; aaa; s main; pdg' ./firmware.bin
```

Outcome: sets the `e` config variable `ghidra.lang` so SLEIGH describes the
binary as 32-bit little-endian x86 regardless of detection, then decompiles
`main` with that language.

*No corpus evidence — derived from upstream documentation.*
