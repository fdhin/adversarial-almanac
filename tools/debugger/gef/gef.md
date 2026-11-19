---
name: "gef"
categories:
  - debugger
homepage: "https://github.com/bata24/gef"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-13"
github-repo: "bata24/gef"
github-stars: 693
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "MIT"
---

# gef

> bata24's fork of GEF — a GDB plugin that layers exploit-dev and kernel-debugging commands on stock GDB, adding heuristic Linux-kernel analysis without symbols, deep glibc/uClibc heap tooling, and wide qemu-user architecture coverage.

## What it's for

This is a fork of the original [GEF](https://github.com/hugsy/gef) (vault note: [[gdb-gef]]) that keeps the familiar GEF experience — checksec, context views, architecture-agnostic commands — and extends it in three directions the original does not cover: heuristic commands for Linux kernel debugging under `qemu-system`/KGDB **without requiring a symbolized `vmlinux`** (kernels 3.x–7.x: page-table walks, kernel memory maps, slab/SLUB inspection), heap dump/analysis commands for multiple allocators (glibc through 2.43, uClibc-ng, and more — including emulated try-malloc/try-free and heap snapshots), and expanded architecture support under `qemu-user`. Canonical-direction note: the original hugsy project remains alive and canonical, and it — not this fork — is what distributions package as `gef`/`gdb-gef` (Kali and nixpkgs `gef` 2026.01, Parrot `gdb-gef`); choose this fork when you need its kernel/heap extras, and the original for the minimal-dependency upstream experience. It is still GDB underneath: every stock command works, the fork just ships more of them.

## Typical use cases

### Orient inside an unknown kernel image

```
gdb -q vmlinux
gef> target remote :1234
gef> kvmmap
gef> pagewalk $pc
```

Outcome: with GDB attached to a `qemu-system` KGDB stub (or `qemu -s` on port 1234) and no symbolized `vmlinux` available, `kvmmap` reconstructs the kernel memory map and `pagewalk` dumps the live page tables for the current address (4/5-level x64, PAE x86, ARM64 including secure-world, ARM LPAE, RISC-V, MIPS, PPC Radix/Hash, s390x, LoongArch, SPARC) — the groundwork for navigating a stripped kernel.

### Inspect slab/SLUB objects in a live kernel

```
gef> slab-contains 0xffff88800abc1234
gef> kmem_cache 0xffff88800abc0000
```

Outcome: `slab-contains` resolves which slab cache (`kmem_cache`) an address belongs to — even for SLUB/SLUB-TINY configurations where fully-allocated slabs hide from `slub-dump` — and `kmem_cache` dumps that cache's metadata; both work on RISC-V without a symbolized vmlinux, and `kmem-cache-alias` reveals alias names.

### Work a glibc heap exploit

```
gef> heap bins-simple -a
gef> heap try-free 0x5555555592a0
gef> heap find-fake-fast 0x555555759000 0x40
```

Outcome: pwndbg-style bins overview for the thread arena (`-a`), an emulated free of a chosen chunk showing the resulting free-list state without corrupting the real heap, and a search for size-like values that could be linked into a fastbin — the standard tcache/fastbin attack prep; `heap snapshot` + `heap snapshot-compare` diff heap state between breakpoints.

### Visualize heap churn during a run

```
gef> heap timeline
gef> heap dump-image
```

Outcome: `timeline` records heap states and serves them at `http://0.0.0.0:61234/` for browsing allocation history live, while `dump-image` renders the chunks of a heap as a composed image — quick ways to spot where corruption begins.

### Debug unusual architectures under qemu-user

```
qemu-or1k -g 1234 ./target-or1k
gdb-multiarch -q ./target-or1k
gef> target remote :1234
```

Outcome: with the fork's expanded qemu-user support (OpenRISC, Alpha, SPARC64 and many more beyond upstream's set), GEF's context views and commands work while single-stepping cross-architecture binaries through the qemu-user gdbstub — useful for embedded firmware userland that nothing else debugs comfortably.

*No corpus evidence — derived from upstream documentation.*
