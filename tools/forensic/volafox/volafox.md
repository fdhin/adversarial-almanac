---
name: "volafox"
categories:
  - blackarch-forensic
version: "143.5b42987"
homepage: "https://github.com/n0fate/volafox"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "n0fate/volafox"
github-stars: 175
github-pushed: "2016-07-25"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# volafox

> Offline macOS memory-forensics toolkit (10.6-10.11): process, kernel-extension, network and keychain recovery plus rootkit-hook checks from a raw memory image.

## What it's for

volafox (by n0fate, GPL2, Python 2) analyzes raw physical memory dumps of Mac OS X Snow Leopard (10.6) through El Capitan (10.11), 32- and 64-bit kernels — firewire or VMware captures, or AFF4 images exported to linear dumps with `rekal aff4export`. It inventories processes, kernel tasks, kernel extensions, mounts and network sockets, dumps process/kernel-extension address space and Mach-O binaries, checks syscall and Mach-trap tables plus KAuth/KDebug/BSM hooks for rootkit tampering, and recovers keychain master key candidates and bash history. Kernel symbol overlays for 10.6-10.11 ship in the repository. Development stopped in 2016, so for newer macOS versions it is the wrong tool; while it lasted it was the purpose-built alternative to running Volatility's mac profiles.

## Typical use cases

### Inventory the system in the image

Identify the OS build and overall state before deeper analysis.

```
python2 vol.py -i memory.dump -o system_profiler
```

Outcome: prints kernel version, CPU and memory specs and boot/sleep/wakeup times; `-o mount`, `-o ps`, `-o kextstat` and `-o netstat` similarly list mounted filesystems, processes, kernel extensions and network sockets.

### List tasks and spot hidden processes

Compare two views of the process world.

```
python2 vol.py -i memory.dump -o tasks
```

Outcome: walks the kernel task list and prints every task; entries present here but missing from `-o ps` output are processes hidden from normal process listings — a classic rootkit sign.

### Check kernel tables for rootkit hooks

Look for syscall or notifier interception.

```
python2 vol.py -i memory.dump -o systab
```

Outcome: prints the syscall table so entries pointing outside the kernel image stand out; `-o mtt` does the same for the Mach trap table, and `-o notifiers` checks I/O Kit notifier hooks.

### Dump a process binary or address space from memory

Recover executable code for offline analysis.

```
python2 vol.py -i memory.dump -o machdump -x 1234
```

Outcome: writes the Mach-O image of PID 1234 from memory (with relocation applied) to a file usable in a disassembler; `-o ps -x 1234` dumps the full process address space instead, and `-o kextstat -x <id>` dumps a kernel extension.

### Recover keychain master key candidates

Enable offline keychain decryption.

```
python2 vol.py -i memory.dump -o keychaindump
```

Outcome: prints master key candidates for decrypting the user's keychain (supported from Lion through El Capitan).

*No corpus evidence — derived from upstream documentation.*
