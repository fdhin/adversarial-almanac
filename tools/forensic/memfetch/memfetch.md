---
name: "memfetch"
categories:
  - blackarch-forensic
version: "1.02"
homepage: "https://lcamtuf.coredump.cx/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-09-14"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# memfetch

> On-demand memory dumper for any live Linux process by Michal Zalewski (lcamtuf) — copies a process's mappings to indexed files without affecting its execution.

*No corpus evidence — derived from upstream documentation.*

## What it's for

memfetch dumps the memory of any live userspace process on Linux, attaching just long enough to copy the mappings out and otherwise leaving the target unaffected. It was originally written by Michal Zalewski in 2002 and revised in 2026 to work on modern 64-bit systems (version 1.02). Typical uses: recovering data such as documents or screen contents from non-responsive programs, capturing process memory for artifact hunting with command-line tools, and peeking into applications that employ anti-debugging features. It can also be told to wait for a fault condition such as SIGSEGV before dumping — useful for privileged processes or custom signal handlers that never produce a normal core dump. It handles one process per invocation, not whole-system RAM (for physical memory see a kernel memory dumper such as memdump).

## Typical use cases

### Dump a process by PID

```
memfetch 1234
```

Outcome: attaches to PID 1234, copies each writable memory mapping to dump files in the current directory, and writes an index file mf-index.txt mapping every dump file to its address range — run strings or grep over the dumps, or use the index to locate a specific address.

### Dump at the moment of a crash

```
memfetch -s 1234
```

Outcome: stays attached until the process receives a fault signal (SIGSEGV, SIGABRT and similar), then dumps its memory — capturing the crash state of processes that would otherwise never write a core file.

### Include read-only mapped files

```
memfetch -a 1234
```

Outcome: also dumps read-only mappings, which mostly contain sections of the original binary and its shared libraries, complementing the default writable-only capture.

### Dump only the mapping containing a specific address

```
memfetch -A 0x7f0000000000 1234
```

Outcome: dumps just the single mapping containing the given hex address instead of the whole address space — targeted capture when you already know where the interesting data lives.
