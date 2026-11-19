---
name: "radare2-unicorn"
categories:
  - blackarch-disassembler
version: "922.7a6e373"
homepage: "https://github.com/radare/radare2-extras/tree/master/unicorn"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-01"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "radare/radare2-extras"
github-stars: 263
github-pushed: "2026-11-04"
github-archived: false
github-issues: 37
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 75
gh-activity-checked: "2026-11-09"
---

# radare2-unicorn

> radare2 plugin that exposes the Unicorn CPU-emulation engine as a debugger backend, so r2's debugger commands drive an emulated CPU instead of a live process.

## What it's for

radare2 can plug different debugger backends (native, gdb, windbg, ...); this package adds Unicorn — the CPU-emulator fork of QEMU — as another one. After selecting it with `dL unicorn`, r2 copies the mapped memory of the opened binary into an emulated CPU instance, and from then on the usual r2 debugger commands (register access, stepping) execute against the emulator. This lets you single-step code on any host without running the real process — handy when the target is unsafe to execute or built for a foreign architecture. It emulates raw CPU code only: there is no OS-level emulation, so syscalls and library calls are not serviced as a real system would do.

## Typical use cases

### Emulate a binary inside r2

Attach the Unicorn backend to the loaded binary.

```
r2 /bin/ls
dL unicorn
dpa
```

Outcome: `dL unicorn` selects the backend and prints the emulated machine's setup (`[UNICORN] Using arch x86 bits 64`, base address, segments, defined stack); `dpa` copies r2's memory state into the emulator. All r2 debugger commands now operate on the emulated CPU.

### Drive execution from the prompt

Set the program counter and step through code.

```
dr rip=entry0
ds
dr=
```

Outcome: sets RIP to the entry point, performs one emulated step (`ds`), and dumps all registers after the step (`dr=`) — repeat the step to walk through stubs and watch registers and memory change under emulation.

*No corpus evidence — derived from upstream documentation.*
