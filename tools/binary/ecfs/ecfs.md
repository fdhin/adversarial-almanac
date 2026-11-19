---
name: "ecfs"
categories:
  - blackarch-binary
version: "305.1758063"
homepage: "https://github.com/elfmaster/ecfs"
packaged-by: [blackarch]
blackarch-builddate: "2018-12-09"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "elfmaster/ecfs"
github-stars: 229
github-pushed: "2019-01-25"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ecfs

> Extended core-file snapshots: Linux crash dumps rebuilt into ELF files that carry full section headers, reconstructed symbols and forensic context.

## What it's for

ECFS (Extended Core File Snapshot), by elfmaster, plugs into the Linux
core-dump path and rebuilds crash snapshots into "ecfs-core" files:
backwards-compatible ELF cores that additionally contain section headers
and fully reconstructed relocation and symbol tables reflecting the
process's runtime state — including the mapped shared libraries — plus
per-thread registers, the exit signal and the open file-descriptor table.
That makes a snapshot far more analyzable than a plain core: load it in IDA
or objdump and see the code exactly as it existed in memory, with symbols,
which is the point for runtime malware analysis, process forensics and
reverse-engineering packed or self-modifying binaries. Two helpers ship
with it: `readecfs`, the readelf equivalent for ecfs files, and `et-flip`,
which toggles a snapshot's e_type between ET_NONE (objdump-friendly) and
ET_CORE (gdb-friendly). The quick, non-killing snapshot utilities
(ecfsnap32/64) exist in the source but are incomplete upstream and are not
shipped by the BlackArch package.

## Typical use cases

### Capture a process snapshot through the core-dump handler

```
mkdir -p /opt/ecfs/cores
echo '|/usr/bin/ecfs-handler -t -e %e -p %p -o /opt/ecfs/cores/%e.%p' > /proc/sys/kernel/core_pattern
kill -SEGV 1234
```

Outcome: with the handler wired into core_pattern, the dumped process is
reconstructed into `/opt/ecfs/cores/<name>.<pid>` — an ecfs-core with
runtime symbols and sections (`-t` pulls in the full text segment of every
shared library; larger dumps, slower capture).

### Dissect a snapshot with readecfs

```
readecfs -e /opt/ecfs/cores/target.1234
```

Outcome: prints the ecfs-specific detail of the snapshot — executable path,
personality flags (PIE, dynamic linking, heuristics), thread count with
register sets per thread, exit signal, the open fd paths and the
shared-library mappings — the forensic overview before deeper analysis.

### Load a snapshot into GDB

```
et-flip /opt/ecfs/cores/target.1234
gdb -q /usr/bin/target /opt/ecfs/cores/target.1234
```

Outcome: the first command flips e_type to ET_CORE so GDB accepts the file
as a core dump; GDB then resolves the crashed executable path, thread
context and crash state (signal, RIP, stack) from the snapshot.

### Disassemble a snapshot in objdump

```
et-flip /opt/ecfs/cores/target.1234
objdump -d /opt/ecfs/cores/target.1234
```

Outcome: flipping back to ET_NONE makes objdump use the section headers,
so the reconstructed text sections — executable plus mapped libraries —
disassemble with symbols instead of being ignored as a header-less core.

*No corpus evidence — derived from upstream documentation.*
