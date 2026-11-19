---
name: "meltdown-git"
categories:
  - scanner
version: "20191026.r84"
homepage: "https://github.com/IAIK/meltdown"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: make
license: custom
meta-checked: "2026-11-09"
github-repo: "IAIK/meltdown"
github-stars: 4168
github-pushed: "2022-05-30"
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

# meltdown-git

> IAIK's Meltdown (CVE-2017-5754) proof-of-concept suite built on libkdump.

## What it's for

The reference PoCs for the original Meltdown paper: a small C library (libkdump) that adapts the transient-execution read primitive to the running CPU, plus demo programs that read memory they should not be able to — their own address space, the kernel's KASLR offsets, and physical memory of other processes. They demonstrate the bug and measure read reliability; they are not a weaponized memory dumper, and on kernels from 2018 onward KPTI plus microcode mitigations usually make them fail or crawl — their value today is reproducing the 2018 findings on unpatched or retro hardware. All demos are timing-based and must be pinned to one core with `taskset`.

## Typical use cases

### Verify the primitive works

Sanity-check the CPU/kernel combination first.

```
make
taskset 0x1 ./test
```

Outcome: builds the demos and reads a known string from the process's own address space via Meltdown; agreeing `Expect:` and `Got:` lines mean the machine is vulnerable — if this fails, the other demos will too.

### Recover the kernel direct-map offset (KASLR)

Get the value the physical-memory demos need.

```
sudo taskset 0x1 ./kaslr
```

Outcome: leaks the randomized direct physical map offset and prints e.g. `[+] Direct physical map offset: 0xffff880000000000` — pass this offset as the argument to the memory-reading demos.

### Measure read reliability

Quantify how usable the leak is on this machine.

```
sudo taskset 0x1 ./reliability 0xffff880000000000
```

Outcome: repeatedly reads physical addresses through the supplied map offset and prints a success rate (e.g. `[-] Success rate: 99.93%`) — the quality metric for any further work with `physical_reader`, which takes the same offset argument.
