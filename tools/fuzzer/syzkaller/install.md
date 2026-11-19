---
tool: "syzkaller"
build-system: "go"
upstream: "https://github.com/google/syzkaller"
recipe: "upstream"
license: "Apache-2.0"
---

# Installing syzkaller

> Coverage-guided kernel fuzzer (syz-manager, syz-executor, syz-execprog, syz-repro).

Source: hacktricks feed; upstream [`google/syzkaller`](https://github.com/google/syzkaller).

## Build from source

Requires the Go toolchain (Go 1.23+):

```bash
git clone https://github.com/google/syzkaller
cd syzkaller
make
```

All compiled binaries land in `bin/` (`syz-manager`, `syz-executor`, `syz-execprog`, `syz-repro`, ...). With Docker available, `./tools/syz-env make` handles the toolchain for you.

Cross-OS/arch testing needs `TARGETOS`, `TARGETVMARCH` and `TARGETARCH` passed to `make` (see the Makefile), and possibly a binutils setup as described in the upstream coverage docs.

## What else a campaign needs (per upstream Linux setup docs)

- A fuzzable kernel image built with KCOV coverage and the right sanitizer (KASAN etc.)
- A VM setup (qemu/gVisor/etc.) and a small rootfs image; syzbot's Buildroot images are downloadable, or build one with the repo's image scripts
- An SSH key the manager can use to reach the VMs
- Full walkthrough: `docs/linux/setup.md` and `docs/setup.md` in the repo; config reference in `docs/configuration.md`

## Notes

- syzkaller binaries are self-contained Go builds; nothing to install system-wide — `bin/` is the deliverable.
- "This is not an official Google product."

Backlinks: [[fuzzer]] · [[syzkaller]]
