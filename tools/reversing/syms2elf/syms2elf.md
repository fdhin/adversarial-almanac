---
name: "syms2elf"
categories:
  - blackarch-reversing
version: "12.329c2ce"
homepage: "https://github.com/danigargu/syms2elf"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "danigargu/syms2elf"
github-stars: 219
github-pushed: "2022-08-17"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# syms2elf

> IDA Pro / radare2 plugin that exports the symbols you recovered during analysis into a copy of the ELF's symbol table.

*No corpus evidence — derived from upstream documentation.*

## What it's for

When you analyze a stripped ELF in IDA Pro or radare2 — auto-analysis, FLIRT signatures, manual renames — those function names exist only inside the project database. syms2elf (Daniel García, Jesús Olmos) writes the recognized functions into the `.symtab` of an output copy of the binary, so any ELF-aware tool (gdb, objdump, other disassemblers) sees the same names without the originating suite. It works on 32- and 64-bit ELF files only (the plugin refuses other formats) and exports functions. It is a plugin, not a standalone CLI: the IDA side is a dialog, the radare2 side is an r2pipe script; the packaged version is Python 2 era, which current radare2/IDA Python 3 environments may need patching for.

## Typical use cases

### Carry IDA-named functions into other tools

Load a stripped ELF in IDA Pro, let auto-analysis finish, rename functions (or apply FLIRT), then invoke the plugin and write a symbolized copy; verify the result with `file`.

```
file ./binary.with.symbols
```

Outcome: the plugin (Edit → Plugins → syms2elf, then choose the output path) writes a copy whose ELF symbol table holds every function IDA recognized — `file` now reports `not stripped`, and `objdump -d`/`gdb` display the recovered names. On a non-ELF input the plugin refuses with "The input file is not a ELF".

### Export renamed symbols from radare2

Install the plugin into radare2's user plugin tree, then export from an analyzed session.

```
r2pm -i syms2elf
```

Outcome: registers the r2pipe-based plugin for the current user; from an analyzed project (`r2 -AA ./binary`) invoke it with the output path — the plugin's own usage line is `$syms2elf <output file>` — and it writes the same symbolized ELF copy using the function names r2 knows. Requires a Python 2 capable r2 environment as shipped upstream.
