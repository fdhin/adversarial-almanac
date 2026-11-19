---
name: "syzkaller"
categories:
  - fuzzer
homepage: "https://github.com/google/syzkaller"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: go
license: Apache-2.0
github-repo: "google/syzkaller"
github-stars: 6410
github-match: "exact"
gh-checked: "2026-11-13"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
---

# syzkaller

> Google's unsupervised coverage-guided kernel fuzzer — mutates syscall programs against disposable VMs to surface kernel crashes and reproducible privilege-escalation primitives.

## What it's for

syzkaller is an unsupervised coverage-guided fuzzer for operating-system kernels, originally built for Linux and now also targeting FreeBSD, Fuchsia, gVisor, NetBSD, OpenBSD, Windows and Darwin/XNU. Syscall interfaces are described in its own declarative language (syzlang), from which it generates, mutates and minimizes syscall sequences; a central `syz-manager` boots throwaway VMs, runs the `syz-executor` agent inside them, feeds executed-program coverage back into the corpus, and restarts crashed VMs. When a crash is found it automatically reproduces and minimizes it, emitting either a syzlang program or a standalone C reproducer — which is exactly what kernel exploit development wants: a deterministic trigger plus the crash log. For offensive work it is the engine behind most public Linux kernel 0-day/LPE discoveries (syzbot, the upstream continuous fuzzing instance, runs it around the clock); it is not a fuzzer for userspace programs — point AFL++-class fuzzers at those. It is a full fuzzing infrastructure rather than a single binary: you supply a kernel image with coverage/sanitizer support plus a rootfs, and the campaign runs for days.

## Typical use cases

### Run a fuzzing campaign

Start the manager against a prepared config.

```
./bin/syz-manager -config my.cfg
```

Outcome: boots the configured VMs, starts generating and executing syscall programs, and serves live statistics (executed programs, coverage, crashes, reproducers) on the HTTP address from the config — by default `http://127.0.0.1:56741`; the log's `executed N, cover N, crashes N, repro N` counters should all be moving, and a non-zero `cover` value confirms coverage feedback is wired up. The config file defines the target kernel image, VM count/type, SSH key and working directory.

### Compile a C reproducer for a crash

Build a syzkaller-generated standalone reproducer.

```
gcc repro.c -lpthread -static -o repro
```

Outcome: produces a static binary you can copy into any test VM (via scp or by baking it into the image) and run as `./repro` to trigger the same kernel crash deterministically — the starting point for turning a crash report into a privilege-escalation primitive; reproducers are also downloadable from syzbot/AppSpot reports with `wget -O repro.c 'https://syzkaller.appspot.com/x/repro.c?x=<id>'`.

### Execute a syzlang reproducer program

Run a syzkaller-DSL crash program inside the target VM.

```
./syz-execprog -enable=all -repeat=0 -procs=6 ./repro.syz
```

Outcome: executes the `repro.syz` syscall program against the live kernel (requires `syz-executor` present in the VM and root); `-enable=all` selects all syscall pseudo-syscalls, `-repeat=0` runs it once, `-procs=6` runs six parallel processes — used when syzkaller could not synthesize a C reproducer and only the syz program exists.

No corpus evidence — derived from upstream documentation.
