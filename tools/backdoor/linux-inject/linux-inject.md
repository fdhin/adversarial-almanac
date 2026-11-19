---
name: "linux-inject"
categories:
  - blackarch-backdoor
version: "100.268d4e4"
homepage: "https://github.com/gaffe23/linux-inject"
packaged-by: [blackarch]
blackarch-builddate: "2019-06-29"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "gaffe23/linux-inject"
github-stars: 1233
github-pushed: "2022-02-23"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# linux-inject

> ptrace-based injector that loads a shared object into an already-running Linux process — the Linux counterpart of CreateRemoteThread-style DLL injection on Windows.

## What it's for

Injecting a `.so` into a live process on x86, x86_64 or ARM, which `LD_PRELOAD` cannot do because it only takes effect at exec time. The tool attaches to the target with `ptrace()` and loads the library with `__libc_dlopen_mode()` from libc, so the target does not need to be linked against libdl. Typical uses: loading a hooking or instrumentation library into a running service without restarting it, and reproducing injection attacks for detection engineering. It is not stealth tooling and has real limits: distributions with Yama ptrace protection block it by default (see the first use case), Debian's libc has historically been incompatible, and large multi-threaded targets can crash on injection.

## Typical use cases

### Permit injection on Yama-restricted distributions

Many distributions ship `ptrace_scope` set so a process cannot ptrace a process it did not create:

```
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

Outcome: ptrace-based injection into unrelated processes becomes possible until the next reboot. Injection attempts that failed silently before will now proceed; reset it to `1` to restore the restriction.

### Inject a shared object by process name

```
./inject -n sample-target sample-library.so
```

Outcome: prints `targeting process "sample-target" with pid <pid>`, attaches, and on success prints `library "sample-library.so" successfully injected` — the library's constructor code now runs inside the target process.

### Inject into an exact PID

```
./inject -p 1234 ./hook.so
```

Outcome: same injection as targeting by name, but pinned to one specific process — the safe choice when several processes share the same name.

### Confirm the library is mapped in the target

```
grep hook.so /proc/$(pgrep sample-target)/maps
```

Outcome: prints the target's `r-xp`/`r--p`/`rw-p` mapping ranges for the injected library if it is loaded; empty output means the library is not present in that process. This is the reliable post-injection check upstream recommends over trusting the injector's exit message.

*No corpus evidence — derived from upstream documentation.*
