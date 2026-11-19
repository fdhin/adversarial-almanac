---
name: "hotpatch"
categories:
  - blackarch-backdoor
version: "90.fd2baf1"
homepage: "http://www.selectiveintellect.com/hotpatch.html"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2020-01-11"
build-system: cmake
license: BSD
meta-checked: "2026-11-09"
github-repo: "vikasnkumar/hotpatch"
github-stars: 373
github-pushed: "2020-01-10"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hotpatch

> Library and `hotpatcher` CLI that inject a shared library into an already-running Linux process via ptrace, then optionally invoke a symbol in it.

## What it's for

hotpatch brings Windows-style `CreateRemoteThread` library loading to Linux: from one process it loads a `.so` into another live process without stopping it, restores the target's original execution state, and can call a function inside the injected library, passing a serialized byte buffer as its argument. Typical legitimate uses are hot-patching a running service you lack source for, adding instrumentation/monitoring threads, or keeping a PID and its file handles alive across a restart; in an offense context it is the classic ".so injection into a live process" primitive. Constraints to know: same-bitness only (a 64-bit build injects into 64-bit processes), you need privileges over the target (root for arbitrary processes), the injected library must be compiled `-fPIC -nostartfiles`, and each library can be injected into a given process only once. It ships as a C API (`hotpatch.h`, `libhotpatch.so`), the `hotpatcher` command-line front end, and a test library `libhotpatchtest.so`.

## Typical use cases

### Verify injection works using the bundled test library

Self-test the whole inject-and-invoke path against your current shell before touching a real target.

```
sudo hotpatcher -vvvv -l /usr/share/hotpatch/test/libhotpatchtest.so -s mysym $$
```

Outcome: hotpatcher injects `libhotpatchtest.so` into the current shell, invokes its `mysym` symbol, and prints verbose (level 4) ptrace detail; success is confirmed by a fresh timestamp line in `/tmp/hotpatchtest.log`.

### Inject a custom library and invoke a function in a running process

Load your own `.so` into a live process by PID and call one of its exported symbols.

```
sudo hotpatcher -l /opt/inject/payload.so -s payload_entry 1234
```

Outcome: the library is mapped into process 1234 and `payload_entry()` runs inside it; the target keeps running with its original state restored. Build the payload with `gcc -fPIC -nostartfiles -shared` or loading may fail.

### Unlock ptrace of non-child processes on Ubuntu

Ubuntu's Yama ptrace_scope blocks injecting into processes that are not your children; relax it for the session.

```
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

Outcome: ptrace restrictions are lifted system-wide until reboot, allowing hotpatcher to attach to arbitrary same-user (or, with sudo, any) processes; reset it to 1 to re-enable the protection.

*No corpus evidence — derived from upstream documentation.*
