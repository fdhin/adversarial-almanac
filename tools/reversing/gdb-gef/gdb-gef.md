---
name: "gdb-gef"
categories:
  - reversing
version: "2026.01-0parrot1"
homepage: "https://hugsy.github.io/gef/"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "hugsy/gef"
github-stars: 8427
github-pushed: "2026-11-09"
github-archived: false
github-issues: 20
github-match: "search"
gh-checked: "2026-11-09"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 20
gh-activity-checked: "2026-11-09"
---

# gdb-gef

> GEF — a single-script GDB plugin that turns stock GDB into an exploit-development and reverse-engineering workbench.

## What it's for

GEF (GDB Enhanced Features) layers a set of Python commands on top of GDB to remove its usual friction during dynamic analysis: binary protection checks, annotated disassembly, register/stack/source context views, glibc heap inspection and memory maps, all available from the normal `gdb` prompt once the script is sourced from `.gdbinit`. It is architecture-agnostic (x86/x64, ARM, MIPS, PowerPC, SPARC) and has no dependencies beyond GDB built with Python bindings. Reach for it whenever you would otherwise re-type the same twenty stock GDB commands to orient yourself on a crashing binary or a target process. It does not replace GDB — every stock command still works, GEF just adds the scaffolding around them.

## Typical use cases

### Check a binary's exploit mitigations

```
gdb -q ./vuln
gef> checksec
```

Outcome: with GEF loaded, `checksec` prints a colored table of the binary's protections (canary, NX, PIE, RELRO, ...), telling you immediately which exploit paths are open.

### Break at an address and run with crafted input

```
gdb -q ./vuln
gef> break *0x401156
gef> r < input.txt
```

Outcome: the program runs with the file redirected to stdin and stops at the chosen instruction; GEF renders its context view (registers, stack, code) around the stop so you can see the crash or the target state at a glance.

### Inspect the glibc heap

```
gef> heap chunks
gef> heap bins tcache
```

Outcome: `heap chunks` lists every heap chunk with its address, size and flags; `heap bins tcache` shows the tcache free-list — enough to follow an allocation or a poisoning attempt chunk by chunk. `vmmap` alongside gives the live memory layout.

### Follow a forked child process

```
gef> set follow-fork-mode child
gef> r -p 8888
```

Outcome: GDB detaches from the parent and keeps debugging the forked child as the program runs with the given arguments, so server-style processes that fork workers stay debuggable.

## Related: the bata24 fork

This note covers the original upstream GEF by hugsy — the canonical project, actively maintained, and the one distributions package as `gef`/`gdb-gef` (Kali's and nixpkgs's `gef` build hugsy/gef; Parrot ships it as `gdb-gef`). An actively-developed fork by bata24 ([[gef]], vault note `debugger/gef`) extends GEF with heuristic Linux-kernel debugging under qemu-system/KGDB without requiring a symbolized `vmlinux`, deeper multi-allocator heap dump/analysis commands, and wider qemu-user architecture coverage — reach for the fork when you need those extras; the two install side by side.
