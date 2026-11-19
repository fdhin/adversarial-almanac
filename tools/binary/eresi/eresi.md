---
name: "eresi"
categories:
  - blackarch-binary
version: "1291.4769c175"
homepage: "https://github.com/thorkill/eresi"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2023-04-09"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "thorkill/eresi"
github-stars: 576
github-pushed: "2020-11-11"
github-archived: false
github-issues: 63
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# eresi

> The ERESI Reverse Engineering Software Interface: a scriptable ELF analysis and instrumentation framework (elfsh, e2dbg, etrace, libelfsh).

## What it's for

ERESI is a modular reverse-engineering framework for ELF binaries on UNIX-like
systems, originally the ELF shell project and packaged here from its maintained
fork. Its core is `libelfsh` (ELF32/ELF64 manipulation) plus `libasm` (IA32
disassembly) and a family of front ends: `elfsh`, an interactive and scriptable
shell for inspecting and rewriting ELF headers, section/program header,
relocation and symbol tables, GOT, CTORS/DTORS and PAX flags; `e2dbg`, an
embedded debugger that runs inside the debuggee instead of using ptrace; and
`etrace`, a tracer built on function redirection. It also ships injection
techniques (ET_REL injection, PLT infection, ALTPLT/EXTPLT) and a
graphviz-backed control-flow module (`modflow`). Development is dormant — the
README still documents the 0.65/0.8 era — and distributions may split the
front ends into separate packages (`elfsh`, `etrace`), with this package
providing the framework and libraries.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Inspect ELF headers and tables interactively

```
elfsh
load ./sample
info
sht
pht
quit
```

Outcome: starts the ELF shell, loads `./sample` into the workspace, and prints
the ELF header summary (`info`), the section header table (`sht`) and the
program header table (`pht`) before exiting. Tables are indexable by name or
number inside the shell.

### Disassemble a symbol and inspect the GOT and relocations

```
elfsh
load ./sample
disasm main
got
rel
```

Outcome: disassembles the range matched by the `main` expression and prints
the GOT and relocation tables — usually enough to locate hooked or imported
functions in the object.

### Read and re-save an object after inspection

```
elfsh
load ./sample
print sample.hdr.entry
save ./sample.modified
quit
```

Outcome: prints the entry-point field of the loaded object via the documented
`file.table[index].field` access path, and `save` writes the object back to
disk as a new file (use after editing fields with the shell's `write` command).

Debugging note: the embedded debugger `e2dbg` shares the same command language
(`break`, `continue`, `step`, `backtrace`, `stack`, `dumpregs`) and runs the
debuggee and debugger in one process, which sidesteps ptrace and works under
PaX-hardened kernels; the `mode` command switches between on-disk and in-memory
views of the target.
