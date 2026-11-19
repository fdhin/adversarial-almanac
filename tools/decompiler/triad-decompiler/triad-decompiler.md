---
name: "triad-decompiler"
categories:
  - decompiler
version: "0.4.3"
homepage: "https://github.com/electrojustin/triad-decompiler"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: make
license: custom
meta-checked: "2026-11-09"
github-repo: "electrojustin/triad-decompiler"
github-stars: 103
github-pushed: "2015-10-22"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# triad-decompiler

> Tiny Capstone-based decompiler that turns x86 and x86-64 ELF executables into pseudo-C, following calls automatically — handy for stripped binaries where you only know an address.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Triad ("TRiad Is A Decompiler", v0.4 alpha) is a small (~2 KLOC) free decompiler for x86/x86-64 ELF executables: it parses the ELF, disassembles with Capstone, groups the code into blocks and emits pseudo-C. Its distinguishing behaviour is following call sites and decompiling the callees, which makes it usable on stripped binaries where you just point it at an address. Partial mode deliberately leaves jumps and comparisons untranslated when full control-flow recovery produces garbage, and there is a plain disassembly mode as a fallback. Scope is narrow and documented as such by the author: ELF only (no PE/Mach-O), x86-family only, gcc-compiled programs work best; switch statements, strings and statically allocated arrays are weakly handled, struct analysis is absent, and crashes are expected — it is an alpha research tool, not a production decompiler. The package installs the binary as `triad`.

## Typical use cases

### Decompile a whole program from main

```
triad -f ./sample
```

Outcome: full decompilation — Triad locates `main` from the symbols and prints pseudo-C for it and every function it calls (`-f` is the default mode). Read the emitted variable declarations, calling-convention recovery and operators to reconstruct what the program does.

### Decompile a stripped binary from an address

```
triad -f ./sample 0x8048474
```

Outcome: with symbols stripped, `main` cannot be found automatically; passing a start address as the second argument begins decompilation there and still follows calls into callees. A wrong address yields wrong or empty output — find the entry with a disassembler first.

### Get readable pieces without trusting the control flow

```
triad -p ./sample
```

Outcome: partial decompilation — variables and parameters are identified, the calling convention is recovered and instructions are translated to C operators, but jumps and comparisons are left as-is on the premise that you can follow them better than the alpha control-flow recovery can.

### Disassemble only, between two addresses

```
triad -d ./sample 0x400560 0x400620
```

Outcome: prints a plain AT&T-syntax disassembly for the given range (start address, optional cutoff address; the default cutoff is the end of the segment containing the entry point) with no decompilation attempted — the fallback when even partial mode mangles the code.

### Decompile one function only, constants in hex

```
triad -s -h ./sample 0x8048474
```

Outcome: `-s` disables call following so only the function at the given address (or `main`) is decompiled, and `-h` prints all constants in hexadecimal — convenient when cross-referencing the output against a disassembler's addresses.
