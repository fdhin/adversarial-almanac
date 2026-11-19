---
name: "qemu-system-x86"
categories:
  - hardware
version: "11.1.3-1"
homepage: "https://www.qemu.org/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali]
arch-builddate: "2026-10-29"
build-system: other
license: GPL-2.0-only WITH Linux-syscall-note, GPL-2.0-only WITH Linux-syscall-note OR BSD-2-Clause, GPL-2.0-only WITH Linux-syscall-note OR BSD-3-Clause, GPL-2.0-or-later OR BSD-3-Clause, GPL-2.0-or-later WITH Linux-syscall-note, LGPL-2.1-only OR BSD-3-Clause
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# qemu-system-x86

> QEMU's full-system x86 emulator — boots whole kernels and disk images on a virtual machine, the workhorse for kernel debugging, exploit development and firmware/OS analysis.

## What it's for

Running an entire guest machine (x86_64 or i386) in userspace on your host: kernel + initrd + root filesystem, or a full disk image, with configurable CPU models, memory, devices and acceleration (KVM). In security work it is the standard cage for dangerous experiments — kernel exploit development against a custom-built kernel, reproducing crashes safely, testing bootloaders and UEFI, snapshotting a VM before detonating malware, or emulating an unsupported CPU feature set (`-cpu` flags like `+smep` toggle mitigations one by one). The `qemu-system-x86_64` binary is the x86_64 machine emulator this package ships; the same QEMU upstream also builds the per-architecture user-mode emulators packaged separately as `qemu-user` — one source tree, two emulation models. This note covers the system-mode side.

## Typical use cases

### Boot a custom kernel with a root filesystem for exploit development

```
qemu-system-x86_64 -kernel bzImage -drive file=rootfs.ext2,format=raw -nographic -append "root=/dev/sda console=ttyS0 nokaslr nopti" -cpu kvm64,+smep
```

Outcome: the VM boots your kernel image with the ext2 filesystem as the root disk, the guest console is wired to your terminal (`-nographic`, hence `console=ttyS0` in the kernel command line), KASLR and PTI are disabled to make addresses deterministic, and SMEP is enabled to match the hardening you are writing the exploit against. Log in on the serial console and test.

### Attach gdb to the booting kernel

Add the debug stub flags to the same command:

```
qemu-system-x86_64 -kernel bzImage -drive file=rootfs.ext2,format=raw -nographic -append "root=/dev/sda console=ttyS0 nokaslr nopti" -cpu kvm64,+smep -s -S
```

Outcome: `-s` opens a GDB server on TCP 1234 (shorthand for `-gdb tcp::1234`) and `-S` freezes the CPU at startup before the first instruction. From another terminal run `gdb vmlinux -ex "target remote :1234" -ex c` — set breakpoints anywhere in boot, then continue. This is the standard kernel-debugging loop for LKM exploits and kernel-heap research.

### Boot a full disk image with fixed memory

```
qemu-system-x86_64 -m 2048 -drive file=disk.qcow2,format=qcow2 -boot c
```

Outcome: a complete OS from a disk image comes up in a graphical window (or with `-nographic` on serial), with 2048 MB of guest RAM — the everyday shape for analyzing an unknown system image, testing installers, or snapshotting (`-snapshot`) before running untrusted software. `-kernel/-initrd` are unnecessary here: the image's own bootloader runs. When a single foreign-architecture binary (not a whole machine) is the target, QEMU's user mode — packaged separately as `qemu-user` — is the lighter tool; this system-mode binary is the one for kernel, device and boot-chain work.
