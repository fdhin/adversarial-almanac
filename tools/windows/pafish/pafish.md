---
name: "pafish"
categories:
  - blackarch-windows
version: "193.b497899"
homepage: "http://www.hoobie.net/pafish/"
packaged-by: [blackarch]
blackarch-builddate: "2021-11-20"
build-system: windows
license: GPL3
meta-checked: "2026-11-09"
github-repo: "a0rtega/pafish"
github-stars: 3945
github-pushed: "2024-06-21"
github-archived: true
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pafish

> Demonstration tool that runs malware-style VM, sandbox and debugger detection checks and reports which ones catch your environment.

## What it's for

Pafish ("paranoid fish", by Alberto Ortega) is a testing tool for malware analysts: it implements the anti-analysis techniques real malware families use — VM artifacts (VMware, VirtualBox, QEMU, Xen, Hyper-V), sandbox products (Sandboxie, Cuckoo), debugger and analysis-tool detection — and prints which checks fired. Run it inside your analysis VM: everything it detects is a leak your sandbox exposes to samples, so it is the standard quick audit for hardening analysis environments. It is a passive demonstration with no arguments and no network activity; it does not exploit or hide anything itself. The upstream repository is archived, so the tool is stable but no longer developed.

## Typical use cases

### Audit an analysis VM

Run the binary inside the environment you use for malware analysis.

```
./pafish.exe
```

Outcome: prints an environment header (Windows version, CPU, hypervisor) followed by one verdict line per check; each `[!]`-marked line is a detection that leaked your VM/sandbox to the sample — those are the artifacts to harden away before real analysis.

### Compare hardening before and after

Re-run the same binary after changing VM settings to measure improvement.

```
./pafish.exe > after.txt
```

Outcome: captures the full check report to after.txt; diff it against a run from before the change to confirm exactly which detections disappeared and which remain.

*No corpus evidence — derived from upstream documentation and source (a0rtega/pafish; main.c output format inspected directly).*
