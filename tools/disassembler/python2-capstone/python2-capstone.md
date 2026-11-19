---
name: "python2-capstone"
categories:
  - blackarch-disassembler
version: "4.0.1"
homepage: "https://www.capstone-engine.org"
packaged-by: [blackarch, arch, kali, nixpkgs]
blackarch-builddate: "2020-01-18"
arch-builddate: "2026-06-05"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "capstone-engine/capstone"
github-stars: 9177
github-pushed: "2026-11-09"
github-archived: false
github-issues: 350
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 195
gh-activity-checked: "2026-11-09"
---

# python2-capstone

> Python 2 bindings for the Capstone disassembly framework — turn raw bytes into instructions with operands, implicit registers, and semantic groups from one architecture-neutral API.

## What it's for

Capstone is a lightweight multi-architecture disassembly engine (x86/x86-64, ARM/Thumb, ARM64, MIPS, PowerPC, SPARC, SystemZ, XCore, M68K, M680X, TMS320C64x); this package is its Python 2 binding, built from the Capstone 4.0.1 source (the project now lives at the capstone-engine GitHub organization). It is a library, not a CLI: you write short Python 2 scripts that hand bytes to a `Cs` object and iterate over the returned instructions, optionally requesting full instruction detail (operands, implicit register reads/writes, instruction groups). Reach for it when disassembling buffers inside a legacy Python 2 toolchain — emulators, unpackers, or analysis pipelines that still run on Python 2. It does not load executables or follow control flow: it linearly disassembles the bytes you give it, from a start address you choose.

## Typical use cases

### Linearly disassemble a buffer

Decode raw x86-64 bytes at a chosen base address.

```
python2 -c "
from capstone import *
code = b'\x55\x48\x8b\x05\xb8\x13\x00\x00'
md = Cs(CS_ARCH_X86, CS_MODE_64)
for insn in md.disasm(code, 0x1000):
    print('0x%x:\t%s\t%s' % (insn.address, insn.mnemonic, insn.op_str))
"
```

Outcome: prints one line per instruction — `0x1000: push rbp`, then the `mov` — with mnemonic and operand string already split into separate fields, ready for further processing. Change the arch/mode constants to decode any supported architecture.

### One-shot disassembly

Skip object setup for a quick look at bytes.

```
python2 -c "
from capstone import *
for insn in cs_disasm_quick(CS_ARCH_X86, CS_MODE_32, b'\xba\xcd\xab\x00\x00\x8d\x4c\x32\x08', 0x1000):
    print('0x%x:\t%s\t%s' % (insn.address, insn.mnemonic, insn.op_str))
"
```

Outcome: the `cs_disasm_quick` helper creates a temporary Cs instance and prints the same address/mnemonic/operand lines with no persistent state — convenient inside one-liners.

### Disassemble with full instruction detail

Request semantic metadata per instruction.

```
python2 -c "
from capstone import *
code = b'\x55\x48\x8b\x05\xb8\x13\x00\x00'
md = Cs(CS_ARCH_X86, CS_MODE_64)
md.detail = True
for insn in md.disasm(code, 0x1000):
    print(insn.id, insn.insn_name(), insn.mnemonic, insn.op_str)
    print([insn.reg_name(r) for r in insn.regs_read])
    print([insn.reg_name(r) for r in insn.regs_write])
    print(insn.groups)
"
```

Outcome: because `detail = True`, each instruction also exposes its numeric instruction ID, its name, the implicit registers read and written (as names, via `reg_name`), and its group IDs — empty for instructions that define none. Without detail mode these fields are not populated.

### Switch x86 output to AT&T syntax

Match objdump/gdb operand style.

```
python2 -c "
from capstone import *
md = Cs(CS_ARCH_X86, CS_MODE_32)
md.syntax = CS_OPT_SYNTAX_ATT
for insn in md.disasm(b'\xba\xcd\xab\x00\x00', 0x1000):
    print(insn.mnemonic, insn.op_str)
"
```

Outcome: operands print in AT&T order (`mov $0xabcd, %edx`) instead of Intel syntax, so output can be diffed directly against objdump or gdb listings.

*No corpus evidence — derived from upstream documentation.*
