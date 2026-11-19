---
name: "lida"
gentoo-category: "dev-util"
version: "03.00.00"
homepage: "http://lida.sourceforge.net/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-1
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# lida

> Linux Interactive DisAssembler: a Perl/Tk tool for browsing, annotating and patching i386 ELF binaries.

## What it's for

lida (the Linux Interactive DisAssembler, by Mario Schallner, last updated 2004) is an interactive disassembler and code-analysis tool for ELF and raw i386 binaries. A compiled backend (`lida_back`, built against the bastard project's libdisasm for opcode decoding) feeds a Perl/Tk GUI that generates a dead listing with string, symbol and cross-reference tables and execution-flow tracing; you rename functions and labels, comment code while debugging the target in another tool, scan for anti-debugging and anti-disassembly tricks and known crypto constants, and patch bytes in place. It is not a debugger and decodes only x86 32-bit code; the project has been dormant since 2004 and needs perl-tk, so on current systems a maintained disassembler is usually the better choice.

## Typical use cases

### Launch the interactive disassembler

Start the GUI and load a binary to work on its annotated listing.

```
lida
```

Outcome: opens the Perl/Tk interface; load an i386 ELF or raw file to generate the disassembly with its string/symbol/cross-reference tables, then trace execution flow, annotate and patch from the menus.

*No corpus evidence — derived from upstream documentation.*
