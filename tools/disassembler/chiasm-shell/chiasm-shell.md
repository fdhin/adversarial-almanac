---
name: "chiasm-shell"
categories:
  - blackarch-disassembler
version: "33.e20ed9f"
homepage: "https://github.com/0xbc/chiasm-shell"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "0xbc/chiasm-shell"
github-stars: 31
github-pushed: "2017-01-27"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# chiasm-shell

> Interactive assembly/disassembly REPL built on Keystone and Capstone, in the spirit of metasm-shell.

## What it's for

chiasm-shell is a Python command-line scratchpad for flipping between assembly text and opcode bytes. At the `asm>` prompt, Keystone assembles what you type into `\x`-escaped machine code; at the `disasm>` prompt, Capstone decodes a byte string back into instructions. Architectures and modes can be switched on the fly, so the same shell handles x86 32/64-bit, ARM, and the other Keystone/Capstone back ends. It was written as a drop-in replacement for metasm-shell.rb that needs neither Ruby nor a Metasploit install. x86 assembly input is Intel syntax only, and it does not load or parse executable files — it works on raw instructions.

## Typical use cases

### Start the shell

Open an interactive session.

```
chiasm-shell
```

Outcome: drops you at the `asm>` prompt (Keystone assembler back end); `switch disasm` flips to the Capstone disassembler prompt, and `help` lists all commands.

### Assemble instructions to opcodes

Encode instructions into opcodes at the `asm>` prompt.

```
inc eax; xor ebx, ebx
```

Outcome: prints the encoded machine code (for this input, `\x40\x31\xdb`) ready to paste into a shellcode stub.

### Disassemble a byte string

Decode opcodes back to instructions at the `disasm>` prompt.

```
\x40\x31\xdb
```

Outcome: prints one instruction per line with synthetic addresses (for this input, `0x1000: inc eax`, `0x1001: xor ebx, ebx`).

### Switch architecture and mode

Point the back end at a different target.

```
setarch x86 64
```

Outcome: reconfigures the active back end to x86 64-bit mode; `lsarch` and `lsmode` list the architectures and modes the current back end supports.

*No corpus evidence — derived from upstream documentation.*
