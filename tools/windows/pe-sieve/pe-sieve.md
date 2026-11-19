---
name: "pe-sieve"
categories:
  - blackarch-windows
version: "0.4.1.1"
homepage: "https://github.com/hasherezade/pe-sieve"
packaged-by: [blackarch]
blackarch-builddate: "2025-09-14"
build-system: windows
license: BSD
meta-checked: "2026-11-09"
github-repo: "hasherezade/pe-sieve"
github-stars: 3935
github-pushed: "2026-06-06"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 73
gh-activity-checked: "2026-11-09"
---

# pe-sieve

> Light-weight Windows process scanner that detects and dumps injected PEs, shellcode, hooks and in-memory patches.

## What it's for

PE-sieve inspects the memory of a single running process (passively — it does not hook or pause the target beyond reading its memory) and reports what does not belong: replaced or injected PE modules, shellcode regions, inline hooks and other in-memory patches. It uncovers the common injection techniques — process hollowing, Doppelgänging, reflective DLL injection — and dumps the implanted material to disk for further analysis, which also makes it a fast dynamic unpacking helper. It is a scanner and collector, not a verdict engine: it flags suspicious artifacts (hooks can be legitimate) and hands you the raw dumps. To sweep every process or the whole system, use its sibling HollowsHunter, which wraps PE-sieve.

## Typical use cases

### Scan one process for implants

Check a running process for in-memory modifications.

```
pe-sieve64.exe /pid 1234
```

Outcome: scans the process and writes any dumped implants plus a report under the process directory; a summary of replaced/injected modules and hooks is printed at the end.

### Dump a payload with a rebuilt import table

Unpack a sample so the dump is loadable in IDA/Ghidra.

```
pe-sieve64.exe /pid 1234 /imp 3
```

Outcome: like the basic scan, but recovered PE dumps get an import table rebuilt from the found IATs (mode 3 builds it from scratch), so imports resolve in a disassembler instead of showing as raw thunks.

### Detect shellcode, not just PE implants

Catch injected code that never forms a PE.

```
pe-sieve64.exe /pid 1234 /shellc
```

Outcome: additionally detects executable memory regions that belong to no module and dumps them as `.shc` files; expect JIT code in .NET processes to appear here too.

### Write dumps and reports to a chosen directory

Collect results in JSON form for scripted triage.

```
pe-sieve64.exe /pid 1234 /dir dumps /json
```

Outcome: all dumps and the scan report land in the `dumps` directory instead of the process folder, and the reports are additionally printed to stdout as JSON for piping into other tooling.

*No corpus evidence — derived from upstream documentation (README and wiki, option tables verified against hasherezade/pe-sieve).*
