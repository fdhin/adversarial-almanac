---
name: "zelos"
categories:
  - blackarch-binary
version: "272.506554d"
homepage: "https://github.com/zeropointdynamics/zelos"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: AGPL3
meta-checked: "2026-11-09"
github-repo: "zeropointdynamics/zelos"
github-stars: 419
github-pushed: "2023-01-31"
github-archived: false
github-issues: 21
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# zelos

> Python binary-emulation platform that runs Linux/ARM/MIPS binaries with all syscalls emulated, producing a sandboxed strace-style trace.

## What it's for

Zelos (Zeropoint Emulated Lightweight Operating System) is a Python-based binary emulation platform: Unicorn provides CPU execution and Zelos re-implements every syscall, so the target binary runs fully isolated — no host effects — while its dynamic behavior is recorded. Use it to quickly assess what a suspicious binary does (file, registry-like and network activity visible as syscall traces) from the command line or from Python scripts with hooks; an IDA plugin overlays recorded traces as instruction comments. Linux x86 (32- and 64-bit), ARM and MIPS binaries are supported. It emulates a single process's userspace, not a whole system image.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Emulate a binary and trace its syscalls

```
zelos suspect.bin
```

Outcome: emulates the binary with default options and prints every emulated syscall (openat, mmap, connect, ...) to stdout — a dynamic-behavior profile without a VM.

### Trace executed instructions

```
zelos --inst --fasttrace suspect.bin
```

Outcome: additionally prints each executed instruction; `--fasttrace` prints only the first execution of each instruction, giving a compact coverage view instead of full voluminous output.

### Save the syscall trace to a file

```
zelos --trace_file /tmp/trace.log suspect.bin arg1 arg2
```

Outcome: writes the syscall trace to /tmp/trace.log instead of stdout; arguments after the binary name are passed to the emulated program as its argv.

### Drive emulation from Python

```python
import zelos

z = zelos.Zelos("suspect.bin")
z.start(timeout=3)
```

Outcome: runs the binary under emulation for at most 3 seconds inside a script, after which state can be inspected or hooks registered for deeper analysis.
