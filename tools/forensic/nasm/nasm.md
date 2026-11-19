---
name: "nasm"
categories:
  - forensics
version: "3.02-1"
homepage: "https://www.nasm.us"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-06-30"
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "netwide-assembler/nasm"
github-stars: 3348
github-pushed: "2026-08-19"
github-archived: false
github-issues: 99
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 205
gh-activity-checked: "2026-11-09"
---

# nasm

> The Netwide Assembler: an x86/x86-64 assembler with Intel syntax, used both for normal object-file builds and for producing raw shellcode bytes.

## What it's for

nasm assembles x86 and x86-64 assembly in clean Intel syntax (`mov eax, 0x10`, `push esp`) and emits a wide range of output formats via `-f` — linkable objects (`elf`, `elf32`, `elf64`, `macho`, `win32`/`win64`) or flat headerless binaries (`bin`). The `bin` mode is what makes it the standard companion in shellcode workflows: it emits exactly the raw opcode bytes an exploit needs, with no headers or relocations. AT&T-flavored constructs do not translate directly, so code lifted from GDB/objdump-style disassembly must be rewritten to Intel syntax before assembling. For single-instruction opcode lookups without writing a file, Metasploit's `msf-nasm_shell` wraps the same assembler behind a `nasm >` prompt (`msf-nasm_shell` then `jmp $` prints the opcodes), and the companion disassembler `ndisasm` (shipped in the same package) reverses raw bytes back to instructions. 2-clause BSD licensed.

## Typical use cases

### Assemble shellcode source to a raw binary

Produce the exact opcode bytes for an exploit payload.

```
nasm -f bin -o shellcode.bin shellcode.asm
```

Outcome: assembles `shellcode.asm` into a flat, headerless binary — pure opcode bytes with no object-file structure. Read it out with `xxd shellcode.bin` and paste the bytes into an exploit; a `BITS 64` (or `BITS 32`) directive at the top of the source selects the architecture.

### Assemble a linkable object file

Build assembly into a normal program or test harness.

```
nasm -f elf64 -o shellcode.o shellcode.asm
```

Outcome: emits a 64-bit ELF object file that links like any other (`gcc -no-pie shellcode.o -o demo` or `ld`); use `-f elf32` for 32-bit objects. Handy for unit-testing shellcode logic in a small C harness before extracting raw bytes.

### Produce a listing with instruction offsets

See where each instruction lands in the emitted binary.

```
nasm -f bin -l shellcode.lst shellcode.asm
```

Outcome: writes `shellcode.lst` alongside the output, listing each source line with its relative offset and assembled bytes — use it to locate jump targets, relative call offsets, and the position of appended data within the payload.
