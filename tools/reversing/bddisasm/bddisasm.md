---
name: "bddisasm"
categories:
  - reversing
version: "1.37.0-0parrot1"
homepage: "https://github.com/bitdefender/bddisasm/"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: cmake
meta-checked: "2026-11-09"
github-repo: "bitdefender/bddisasm"
github-stars: 1089
github-pushed: "2026-11-03"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 16
gh-activity-checked: "2026-11-09"
---

# bddisasm

> Bitdefender's lightweight x86/x64 instruction decoder library, packaged with the `disasmtool` command-line decoder and shellcode-emulator front-end.

## What it's for

bddisasm is Bitdefender's C library for decoding x86 and x64 instructions in 16-, 32- and 64-bit modes, exposing rich per-instruction metadata: explicit and implicit operands, operand access modes, CPUID feature flags and flags access. It has no external dependencies, allocates no memory and is thread-safe, so it is meant to be linked into analysis tools rather than driven by hand. The package ships the libraries — including bdshemu, a small emulator aimed at shellcodes — plus one CLI, `disasmtool`, which decodes raw files or hex strings and can run shellcodes under bdshemu to print an emulation trace. Upstream explicitly bills disasmtool as a demo and test vehicle whose interface may change, not a production disassembler.

## Typical use cases

### Disassemble a raw code blob

Decode a file of native code.

```
disasmtool -b64 -f sample.bin
```

Outcome: prints one line per decoded instruction (address, bytes, mnemonic, operands); use `-b32` or `-b16` for 32- or 16-bit code.

### Disassemble a hex string with extended metadata

Decode bytes pasted as hex without creating a file.

```
disasmtool -b64 -h 909033C0 -exi
```

Outcome: decodes the hex string (plain `33C090CCC3` or escaped `\x33\xC0...` forms accepted) and prints extended per-instruction information; `-hl` adds color-highlighted instruction fields and `-bits` the raw encoding bitfields.

### Emulate a shellcode

Run suspicious shellcode inside the bundled bdshemu emulator.

```
disasmtool shemu -b32 -f shell.bin
```

Outcome: prints an instruction-by-instruction emulation trace (registers, memory accesses) without executing the code natively; add `-k` for kernel-mode emulation or `-bw` to tolerate self-modifying code.

### Emulate with pre-seeded registers

Control the initial register state of the emulated shellcode.

```
disasmtool shemu -b32 -f shell.bin -rax 0x100 -rcx 0xABCD -rsp 0x1000
```

Outcome: the same emulation trace, but execution starts with the supplied register values — often what a stub needs to reach its decoder loop.

### Decode from an offset at a chosen address

Skip headers or alignment padding before disassembling.

```
disasmtool -b64 -o 16 -r 0x401000 -f sample.bin
```

Outcome: starts decoding 16 bytes into the file and reports instruction addresses from 0x401000 onward; `-vintel`/`-vamd` restrict decoding to Intel- or AMD-specific encodings.

*No corpus evidence — derived from upstream documentation.*
