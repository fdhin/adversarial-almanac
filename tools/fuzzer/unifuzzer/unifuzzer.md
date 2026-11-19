---
name: "unifuzzer"
categories:
  - blackarch-fuzzer
version: "5.3385a3b"
homepage: "https://github.com/rk700/uniFuzzer"
packaged-by: [blackarch]
blackarch-builddate: "2020-02-17"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "rk700/uniFuzzer"
github-stars: 347
github-pushed: "2019-08-23"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# unifuzzer

> Coverage-guided fuzzing of closed-source ARM/MIPS firmware binaries via Unicorn emulation.

## What it's for

uniFuzzer emulates 32-bit LSB ELF binaries for ARM and MIPS — the closed-source blobs typical of IoT firmware — inside the Unicorn CPU emulator and drives them with libFuzzer, giving coverage-guided fuzzing without target source code. You reverse the binary, pick an interesting function, and describe how to load and call it in a small C callback file (`onLibLoad`, `uniFuzzerInit`, `uniFuzzerBeforeExec`, `uniFuzzerAfterExec`); dependent libraries are resolved and loaded automatically, and library calls can be overridden with a preload library. It is a research tool whose last upstream activity was 2019, and it does not support syscalls, DT_GNU_HASH symbol lookup or IFUNC.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Fuzz a chosen function in a closed-source binary

Run the emulator-backed fuzzer against a target ELF once the callback file for the function under test is in place.

```
UF_TARGET=./target.elf UF_LIBPATH=/path/to/firmware/libs ./uf
```

Outcome: libFuzzer starts mutating inputs and executing the target under Unicorn, reporting coverage edges and saving crashing inputs; the target's dependent libraries are loaded automatically from `UF_LIBPATH`.

### Override library calls during emulation

Stub out noisy or heavy library functions by preloading a same-architecture shared object.

```
UF_TARGET=./target.elf UF_PRELOAD=./hook.so UF_LIBPATH=/path/to/firmware/libs ./uf
```

Outcome: calls the preload library defines (for example an empty `printf` or simplified `malloc`/`free`) are served by the hook instead of the firmware's real library, keeping fuzzing throughput high.

### Point the loader at several library directories

Give the loader multiple colon-separated search paths for dependency resolution.

```
UF_TARGET=./target.elf UF_LIBPATH=/firmware/usr/lib:/firmware/lib ./uf
```

Outcome: the loader resolves the target's imported libraries across every listed directory before execution starts, so a partially extracted firmware image is enough to begin fuzzing.
