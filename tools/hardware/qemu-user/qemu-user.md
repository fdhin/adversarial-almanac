---
name: "qemu-user"
categories:
  - hardware
version: "11.1.3-1"
homepage: "https://www.qemu.org/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-29"
build-system: other
license: GPL-2.0-only WITH Linux-syscall-note, GPL-2.0-only WITH Linux-syscall-note OR BSD-2-Clause, GPL-2.0-only WITH Linux-syscall-note OR BSD-3-Clause, GPL-2.0-or-later OR BSD-3-Clause, GPL-2.0-or-later WITH Linux-syscall-note, LGPL-2.1-only OR BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "qemu/qemu"
github-stars: 14002
github-pushed: "2026-11-09"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10095
gh-activity-checked: "2026-11-09"
---

# qemu-user

> QEMU user-mode emulation — runs a single foreign-architecture Linux binary on your host by translating its machine code and syscalling natively, no guest OS required.

## What it's for

Executing one ARM/AArch64/MIPS/PowerPC/RISC-V/... ELF on an x86_64 Linux host (or any other pairing) without booting a whole virtual machine: QEMU translates the binary's instructions on the fly and forwards its system calls to the host kernel. That is the cheap way to run cross-compiled tools, reproduce an architecture-specific crash under your own gdb/strace, or quickly triage a foreign binary before committing to full emulation — the dynamic-analysis counterpart to static disassembly. The catch is the dynamic loader and libraries: the binary expects its own architecture's `/lib/ld-linux-...`, so you point QEMU at a root filesystem of the target architecture with `-L` (this package ships static emulator binaries, so the emulator itself runs anywhere). It emulates Linux user space only — syscalls, one process at a time; for kernels, boot chains and devices you need the system-mode emulators from the same upstream, packaged as `qemu-system-x86` and its siblings (`qemu-system-*`). Both packages are built from the single QEMU source tree at qemu.org; there is no separate qemu-user upstream project.

GitHub: qemu/qemu is the official read-only mirror of the canonical repository at gitlab.com/qemu-project/qemu.

## Typical use cases

### Run a foreign-architecture binary with its own libraries

```
qemu-arm -L /usr/arm-linux-gnueabihf ./target_binary
```

Outcome: the ARM ELF executes under translation; `-L` sets the ELF interpreter prefix so the binary loads the ARM dynamic loader and libraries from the given sysroot instead of failing with "no such file or directory". Any output it prints appears on your terminal as if it were native.

### Run a static binary with no sysroot at all

```
qemu-aarch64 ./static_backdoor_analysis_binary
```

Outcome: because a statically linked binary embeds its libc, no `-L` root is needed — the binary just runs. This is the quickest "what does it actually do" step for a foreign-architecture sample: observe files, sockets and output on your own host, ideally inside a sandbox.

### Debug a foreign binary with your native gdb

```
qemu-aarch64 -g 1234 ./target_binary
```

Outcome: QEMU starts the binary but holds it until a GDB remote connection arrives on port 1234; from another terminal run `gdb-multiarch -ex "target remote :1234"` (with the matching architecture set) and single-step the translated code — cross-architecture crash triage without a full system emulation setup.

### Emulate a big-endian target the same way

```
qemu-ppc64 -L /usr/ppc64-linux-gnu ./setup_tool
```

Outcome: same pattern as the ARM case but for big-endian PowerPC — the interpreter prefix (`-L`, or the `QEMU_LD_PREFIX` environment variable for the same effect) is the one option that matters whenever the target is dynamically linked.

*No corpus evidence — derived from upstream documentation.*
