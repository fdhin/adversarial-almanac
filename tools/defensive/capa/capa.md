---
name: "capa"
categories:
  - blackarch-defensive
version: "7.4.0"
homepage: "https://github.com/mandiant/capa"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-11-03"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "mandiant/capa"
github-stars: 6302
github-pushed: "2026-11-06"
github-archived: false
github-issues: 320
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 529
gh-activity-checked: "2026-11-09"
---

# capa

> Identifies what an executable can do — capabilities, MITRE ATT&CK techniques and MBC behaviors — through static rule matching.

## What it's for

capa (Mandiant FLARE) statically analyzes executables (PE, ELF, .NET, shellcode) and reports the capabilities their code implements — for example acting as a backdoor, installing services, or communicating over HTTP — instead of only a family label. Results are grounded: every capability traces back to the rules that matched and, at higher verbosity, to the code locations that satisfied them. The standalone release binaries embed the rule set, so no separate rules download is required. capa also accepts sandbox report JSON (for example from CAPE) to derive capabilities from observed dynamic behavior, which complements static analysis on packed samples. It is not a sandbox, antivirus scanner or unpacker.

## Typical use cases

### Triage an unknown binary

Get a capability-first overview of a suspicious sample.

```
capa suspicious.exe
```

Outcome: a table of matched ATT&CK tactics/techniques followed by a tree of capabilities grouped by namespace (for example c2/shell), enough to judge whether the file looks like a loader, RAT or benign utility.

### Show where each capability was found

Get rule-level evidence with match locations for reporting.

```
capa -vv suspicious.exe
```

Outcome: beneath each capability, the specific rules that matched and the virtual addresses or disassembly snippets that satisfied them, letting an analyst verify findings instead of trusting a label.

### Analyze a packed sample via its sandbox report

Recover behavior that static analysis cannot see through packing.

```
capa report.json
```

Outcome: capa parses the sandbox report JSON and prints capabilities derived from the observed dynamic behavior; on packed executables capa itself warns that static results may be incomplete and points to this workflow.

*No corpus evidence — derived from upstream documentation.*
