---
name: "pmdump"
categories:
  - blackarch-windows
version: "1.2"
homepage: "https://vidstromlabs.com/freetools/pmdump/"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-03"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# pmdump

> PMDump — dump a running Windows process's memory to a file without stopping the process (Vidstrom Labs).

## What it's for

PMDump (Arne Vidstrom, Vidstrom Labs — the tool line that began on ntsecurity.nu) copies the memory contents of a live process out to a file while the process keeps running, enabling the classic incident-response and credential-hunting move of grabbing memory (secrets, keys, injected code) without the disruption of suspending or killing the target — sometimes the only option when stopping the process would trip a watchdog or destroy evidence. It enables SeDebugPrivilege for itself, so it must run elevated and can dump processes you own and, with the right privileges, system processes; it reads the target's address space and writes only the committed regions by default. It is a three-form console utility (dump, dump-with-holes, list) with no options beyond that — it does not parse, scan or extract anything from the dump; hand the output to a memory-forensics tool afterwards. It is distributed from vidstromlabs.com as a standalone exe; there is no canonical GitHub repository (same-named GitHub projects are unrelated).

## Typical use cases

### Dump a process's committed memory

The core operation: pick a PID, name the output file.

```
pmdump.exe 1234 lsass.dmp
```

Outcome: opens the process with debug rights and writes its memory contents to `lsass.dmp` while the process keeps running. It refuses to overwrite an existing output file ("Error: The output file already exists") — choose a fresh filename; a wrong PID reports `Error: Cannot find that PID`, and failures to open the target mean the console is not elevated.

### List processes and their PIDs

Find the PID to dump.

```
pmdump.exe -list
```

Outcome: prints all running processes with their PIDs (system idle process shown as PID 0) — the companion step before a dump.

### Dump the full address space including holes

Preserve the exact address-space layout, not just committed regions.

```
pmdump.exe 1234 full.dmp -full
```

Outcome: writes the dump with holes included and zero-filled, so file offsets line up with the complete virtual address space — the form to use when later analysis depends on addresses mapping directly into the dump.

*No corpus evidence — derived from upstream documentation and the packaged binary's usage output.*
