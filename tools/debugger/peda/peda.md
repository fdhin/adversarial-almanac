---
name: "peda"
categories:
  - debugger
homepage: "https://github.com/longld/peda"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-13"
github-repo: "longld/peda"
github-stars: 6171
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "custom:unknown"
github-commits-year: 0
gh-activity-checked: "2026-11-13"
---

# peda

> PEDA — the classic single-file GDB plugin that adds exploit-development commands to stock GDB: cyclic patterns, checksec, ROP gadget hunting, memory search and shellcode generation.

## What it's for

PEDA (Python Exploit Development Assistance for GDB) is the original GDB enhancement plugin that pioneered the workflow later reused by GEF and pwndbg: sourced from `.gdbinit`, it colorizes the debugging display (disassembly, registers, stack) and adds the commands exploit developers re-type constantly — binary protection checks, cyclic (De Bruijn) pattern creation and offset lookup, ROP gadget search across memory, `/proc` info, memory search/patch/XOR, and shellcode generation. Reach for it on legacy toolchains or muscle-memory workflows built around its command names; note that upstream is dormant (last push 2024, no commits in the past year) and historically targets GDB with Python 2 bindings, so on modern Python-3-only GDB builds the community fork (zachriggle/peda) or its successors GEF/pwndbg are usually the smoother choice. It extends GDB; every stock command still works underneath.

## Typical use cases

### Install into GDB

```
git clone https://github.com/longld/peda.git ~/peda
echo "source ~/peda/peda.py" >> ~/.gdbinit
gdb -q ./vuln
```

Outcome: PEDA loads on the next GDB start and replaces the default context with its colorized register/disassembly/stack view; `peda help` from the GDB prompt lists every added command.

### Check a binary's exploit mitigations

```
gdb -q ./vuln
peda> checksec
```

Outcome: prints CANARY, NX, PIE, RELRO and FORTIFY status for the loaded ELF — the pre-flight check before choosing an exploit strategy (stack-smash vs ROP vs return-to-plt).

### Find the crash offset with a cyclic pattern

```
peda> pattern create 200
peda> run
peda> pattern search
```

Outcome: a 200-byte De Bruijn sequence is written out, fed to the vulnerable read after `run`, and once the program crashes `pattern search` scans registers and memory for pattern fragments and reports the exact offset that landed in EIP/RIP (`pattern offset <value>` does the same for one specific value).

### Hunt ROP gadgets

```
peda> ropgadget
peda> dumprop 0x08048000 0x0804b000
```

Outcome: `ropgadget` lists the common ROP gadgets of the binary or a loaded library, while `dumprop` dumps every gadget inside an explicit memory range (from `vmmap`) — the raw material for building a chain, with `lookup` available to find pointers/references into a chosen range.

### Inspect and manipulate process memory

```
peda> vmmap
peda> find "/bin/sh" libc
peda> xormem 0x8048000 0x8048100 0x41
```

Outcome: `vmmap` lists the process's virtual mapping ranges, `find`/`searchmem` locates a string (regex supported) in a mapped section such as libc, and `xormem` decodes a memory region in place — together covering the locate-then-use steps of most exploit chains; `patch` writes string/hex/int data to an address, and `dumpargs` shows the arguments of a call instruction when stopped on one.

*No corpus evidence — derived from upstream documentation.*
