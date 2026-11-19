---
name: "elidecode"
categories:
  - blackarch-reversing
version: "48.38fa5ba"
homepage: "https://github.com/DeveloppSoft/EliDecode"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "DeveloppSoft/EliDecode"
github-stars: 54
github-pushed: "2016-06-19"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# elidecode

> Python 2 utility that emulates obfuscated shellcode with Unicorn and prints the decoded instructions through Capstone.

## What it's for

EliDecode (a fork of unicorn-decoder) takes a raw obfuscated shellcode and runs it under the Unicorn CPU emulator, letting decryption and unpacking stubs execute harmlessly, then disassembles the resulting bytes with Capstone so the real payload can be read instead of the wrapper. It covers x86 (16/32/64-bit), ARM (Thumb/32/64) and MIPS (several revisions, both endiannesses). The upstream repository has been idle since 2016 and the code is Python 2 era; the packaged launcher runs `decoder.py` under python2.

## Typical use cases

### Decode a shellcode with defaults

Emulate an x86 32-bit shellcode and print its unpacked instructions.

```
elidecode -f shell.bin
```

Outcome: emulates shell.bin (default mode `x86_32`), lets the stub decode itself and prints the resulting assembly via Capstone.

### List supported emulation modes

See the exact mode names before selecting an architecture.

```
elidecode --show-modes
```

Outcome: prints all emulation modes — `x86_16`/`x86_32`/`x86_64`, `arm_thumb`/`arm32`/`arm64`, and `mips3`/`mips32`/`mips32R6`/`mips64` in little- and big-endian variants.

### Decode non-x86 shellcode

Pick the architecture matching the sample.

```
elidecode -f shell.bin -m arm_thumb
```

Outcome: emulates the shellcode as 32-bit ARM Thumb code and prints the decoded assembly for that mode.

### Dump the decoded payload to a file

Save the unpacked shellcode bytes for further analysis.

```
elidecode -f shell.bin -o decoded.bin
```

Outcome: writes the binary dump of the decoded, de-obfuscated shellcode to decoded.bin, ready for static tooling; add `-d` to enable extra emulation hooks while debugging the stub, or `-i` to cap the number of emulated instructions.

*No corpus evidence — derived from upstream documentation.*
