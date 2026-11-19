---
name: "python2-minidump"
categories:
  - blackarch-windows
version: "19.749e6da"
homepage: "https://github.com/skelsec/minidump"
packaged-by: [blackarch, arch]
blackarch-builddate: "2019-04-12"
arch-builddate: "2025-12-14"
meta-checked: "2026-11-09"
github-repo: "skelsec/minidump"
github-stars: 309
github-pushed: "2026-08-18"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# python2-minidump

> Parses Microsoft minidump files — one-shot info dump or an interactive memory-browsing shell — and doubles as a Python parsing library.

## What it's for

minidump (by skelsec) is a Python library for the Microsoft minidump format with a small command-line tool of the same name to show it off. Console mode prints everything the parser extracted from a dump in one shot; shell mode drops you into an interactive session where you can enumerate modules, threads and exceptions and browse the dumped process's virtual memory (read / read int / read uint / move / peek / tell). Programmatic use centers on the MinidumpFileReader and MinidumpBufferedReader objects, which read and search the process's virtual address space with CPU-architecture-aware integer sizes. On Windows, the createminidump.py script in the utils folder creates minidumps of live processes via the Windows API, including processes in another user's context when run with SeDebugPrivilege. This vault entry tracks the Python 2 era package of the tool; the same interface is maintained upstream for Python 3.

## Typical use cases

### One-shot parse of a minidump

```
minidump --all lsass.dmp
```

Outcome: prints a text report of everything extracted from the dump — system info, loaded modules with paths and timestamps, threads, exception data and memory descriptors — for a quick look at what the file contains.

### Browse a dump interactively

```
minidump -i lsass.dmp
```

Outcome: opens the interactive shell; type `?` to list commands, then enumerate modules/threads/exceptions and read the dumped process's memory at chosen virtual addresses (read, read int, read uint, move, peek, tell).

*No corpus evidence — derived from upstream documentation.*
