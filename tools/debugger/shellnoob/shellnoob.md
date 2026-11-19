---
name: "shellnoob"
categories:
  - blackarch-debugger
version: "37.6586133"
homepage: "https://github.com/reyammer/shellnoob"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2025-07-05"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "reyammer/shellnoob"
github-stars: 700
github-pushed: "2025-06-30"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shellnoob

> Single-file Python toolkit for writing shellcode: convert between assembly, raw bytes, hex, and embeddable C/Python/Ruby sources, resolve syscalls, and debug shellcode with strace or gdb.

*No corpus evidence — derived from upstream documentation.*

## What it's for

ShellNoob removes the boring, error-prone half of writing shellcode: hand-assembling instructions, hexdumps, escaping bytes, and remembering syscall numbers. One self-contained Python script (Python 2.7 and 3) converts shellcode between formats — assembly, raw binary, hex strings, ELF objects/executables, ready-to-paste C/Python/Ruby/Bash arrays — in any input/output combination, with `--intel` for Intel syntax and `--64` for x86_64. It also resolves syscall numbers, constants, and errno names for the platform, offers an interactive asm-to-opcode REPL for checking which byte sequences are safe to use, patches binaries, and can pipe shellcode straight into strace or gdb. It relies only on gcc/as/objdump plus Python. It is a writing/debugging aid — it does not exploit anything by itself and has no knowledge of specific targets.

## Typical use cases

### Convert assembly to raw shellcode bytes

```
shellnoob --from-asm shell.asm --to-bin shell.bin
```

Outcome: assembles the assembly file and writes the raw instruction bytes to `shell.bin`, ready to drop into an exploit. Use `-` as a filename to read stdin or write stdout, e.g. `cat shell.asm | shellnoob --from-asm - --to-bin - > shell.bin`.

### Turn raw bytes into an embeddable source file

```
shellnoob --from-hex 41424344 --to-c
shellnoob --from-bin shell.bin --to-python
```

Outcome: prints (or writes, with a path argument) the shellcode as a `char[]` for C, or a Python/Ruby/Bash byte-string — the format you paste into the exploit's source. `--to-completec` emits a compilable C program that marks the shellcode page RWX; `--to-safeasm` emits assembly that is guaranteed assemblable when objdump output is not.

### Resolve syscall numbers, constants, and errno

```
shellnoob --get-sysnum read
shellnoob --get-const O_CREAT
shellnoob --get-errno EACCES
```

Outcome: prints the value per architecture — e.g. `read` is `i386 ~> 3`, `x86_64 ~> 0`; `O_CREAT ~> 64`; `EACCES ~> Permission denied`. Works in both directions for errnos (name to description, number to description).

### Interactively test which opcodes encode which bytes

```
shellnoob -i --to-opcode
shellnoob -i --to-asm
```

Outcome: an interactive mode prompt; type `mov %eax, %ebx` and get `89c3`, or paste `89c3` and get the disassembly. Use it to pick instructions that avoid bytes your delivery channel forbids (null bytes, newlines).

### Debug shellcode with strace or gdb

```
shellnoob open-read-write.asm --to-strace
shellnoob open-read-write.asm --to-gdb
```

Outcome: builds the assembly into a temporary executable and runs it under strace (showing every syscall the shellcode makes) or under gdb with a breakpoint already set on the shellcode's entry point. `-c shell.asm --to-exe shell` prepends an `int3` trap so `gdb ./shell; run` stops exactly at your first instruction.

### Pull a shellcode from the shell-storm database

```
shellnoob --from-shellstorm <shellcode_id> --to-asm
```

Outcome: downloads the given shellcode by its ID from the shell-storm database and converts it to the requested format — useful for inspecting or adapting a published sample before trusting it.

### Patch an executable in place

```
shellnoob --file-patch target.bin 0x400 90
shellnoob --vm-patch target.bin 0x08048054 90
shellnoob --fork-nopper target.bin
```

Outcome: writes the hex bytes (`90` = NOP) at a file offset (`--file-patch`) or a virtual address (`--vm-patch`); `--fork-nopper` NOPs out every call to `fork()` in the binary. x86/x86_64 only.

### Use it as a Python library

```python
from shellnoob import ShellNoob

sn = ShellNoob(flag_intel=True)
sn.asm_to_hex('nop; mov ebx,eax; xor edx,edx')
sn.do_resolve_syscall('fork')
```

Outcome: returns `'9089c331d2'` and `{'i386': 2, 'x86_64': 57}` respectively — conversion and resolution available programmatically inside build scripts.
