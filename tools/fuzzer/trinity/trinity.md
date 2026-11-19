---
name: "trinity"
categories:
  - blackarch-fuzzer
version: "5192.e7187245"
homepage: "http://codemonkey.org.uk/projects/trinity/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2023-07-23"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "kernelslacker/trinity"
github-stars: 909
github-pushed: "2026-11-07"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 11414
gh-activity-checked: "2026-11-09"
---

# trinity

> Dave Jones' Linux system-call fuzzer, which feeds semi-intelligent arguments to every syscall it knows on your kernel.

## What it's for

Trinity bombards the Linux kernel with system calls whose arguments are not pure garbage: it passes real file descriptors harvested from /sys, /proc and /dev, valid flag sets (with occasional bit flips) and range-biased values, so calls get past basic validation and deeper into the kernel than naive fuzzers reach. It historically found many kernel bugs and still tracks new syscalls as they land upstream, but the author now describes it as abandonware — recent commits are LLM-generated and any given day's tree may be broken — and recommends kernel-upstream syscall fuzzing for stable use. It can seriously corrupt files, including on mounted network shares, and may emit disruptive network packets: run it only in a throwaway VM against a kernel you control, with `--max-runtime` to bound a run and `-x` to steer around known-buggy syscalls.

## Typical use cases

### Hammer a single syscall

```
trinity -c splice
```

Outcome: stresses the splice syscall with randomly generated, datatype-aware argument sets — the standard way to shake out bugs in a specific code path; the flag can be repeated to target a small set.

### Exclude a syscall that keeps crashing the VM

```
trinity -x splice
```

Outcome: runs the full syscall sweep while never invoking splice, letting a campaign continue past a known kernel bug; repeatable to exclude several syscalls at once.

### Scale the fuzzing load

```
trinity -C16
```

Outcome: spawns 16 child processes fuzzing in parallel (with cgroup-based memory containment by default), raising syscall throughput on a larger VM.

### Reproduce a run from a fixed seed

```
trinity -s 1234567
```

Outcome: seeds the random number generator so the generated argument sequence can be replayed when retesting or filing a bug (the README notes same-seed runs are not yet 100% identical).

### Survey available syscalls before targeting

```
trinity -L
```

Outcome: lists every syscall trinity knows on this kernel and architecture with its offsets — useful to confirm a newly added syscall is covered before singling it out with `-c`; `--dry-run` parses arguments and exits without fuzzing.

*No corpus evidence — derived from upstream documentation.*
