---
name: "pwndbg"
categories:
  - debugger
homepage: "https://github.com/pwndbg/pwndbg"
origin: "pentest-book"
recipe: "upstream"
packaged-by: [arch]
build-system: python
license: MIT
github-repo: "pwndbg/pwndbg"
github-stars: 11197
github-match: "exact"
gh-checked: "2026-11-13"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
---

# pwndbg

> A GDB (and LLDB) plug-in that makes debugging suck less: memory maps, heap inspection, pattern/cyclic helpers and security triage aimed at exploit developers and reverse engineers.

## What it's for

Pwndbg is a Python module loaded into GDB (or run as a REPL interface for LLDB) that fills the gaps vanilla debuggers leave for low-level software development, hardware hacking, reverse engineering and exploit development. Once loaded, plain GDB gains high-level commands — process memory maps, glibc heap visualization, cyclic-pattern creation and lookup, binary protection triage, memory search — so exploit work stops being `x/30gx $rsp` archaeology. It supports GDB 12.1+ and (experimentally) LLDB 19+, and covers Linux binaries/ELF, Mach-O on macOS, kernel debugging via qemu-system, user-space emulation via qemu-user, and embedded ARM Cortex-M/RISC-V targets. It is a debugger enhancement, not a standalone debugger: you still launch `gdb`, pwndbg just extends it. The project publishes a printable cheatsheet and full docs at pwndbg.re.

## Typical use cases

### Triage a binary's mitigations

Check hardening of the loaded file (or any file) before planning an exploit.

```
checksec -f ./vuln
```

Outcome: prints the binary's security settings (NX, canary, RELRO, PIE and friends) as reported by the bundled checksec; inside an active GDB session plain `checksec` does the same for the file already loaded.

### Start at the program's real entry

Skip breakpoint bookkeeping and land where user code begins.

```
start
```

Outcome: runs the debugged program, injecting a temporary stop at the first convenient location found among `main`, `_main`, `start`, `_start`, `init`, `_init` — arguments after the command are passed to the program.

### Read the process memory map

See exactly what is mapped, with permissions, at a glance.

```
vmmap
```

Outcome: prints the virtual memory map pages of the running process (writable/executable regions, stack, heap, shared libraries); pass an address or mapping name to filter, `--gaps` to show unmapped holes.

### Inspect the glibc heap

Walk chunks of the current arena during a heap exploitation session.

```
vis-heap-chunks
```

Outcome: renders the heap's chunks inline with headers, sizes and free-list state, defaulting to the current arena's active heap — the companion `heap` command iteratively prints chunk records (`-c COUNT`, `-v` for verbose) between two addresses.

### Find the offset that smashed a register

Map a crash-time register value back to its cyclic-pattern position.

```
cyclic -l 0x6161616c
```

Outcome: looks up the four bytes found in the crashed register (e.g. EIP after sending a `cyclic()` pattern as input) and prints the byte offset where that value sits in the pattern — that number is the padding length for the payload. Run `cyclic 200` beforehand to generate the pattern that goes into the vulnerable field.

### Search process memory for a value

Hunt strings or words across the mapped regions.

```
search -t string PASSCODE
```

Outcome: scans process mappings for the given string, printing match addresses and surrounding context; `-t` selects the value type (`byte`, `word`, `pointer`, `string`, `asm`, ...), and an optional mapping name restricts the sweep to one region.

No corpus evidence — derived from upstream documentation.
