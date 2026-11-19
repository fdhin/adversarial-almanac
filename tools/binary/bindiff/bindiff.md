---
name: "bindiff"
categories:
  - blackarch-binary
version: "6.0.0"
homepage: "http://www.zynamics.com/bindiff.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2020-03-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "google/bindiff"
github-stars: 3233
github-pushed: "2026-09-18"
github-archived: false
github-issues: 53
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 27
gh-activity-checked: "2026-11-09"
---

# bindiff

> Graph-based comparison of two disassembled binaries: matches functions, rates similarity, and isolates exactly what changed between versions.

## What it's for

BinDiff (developed by zynamics, part of Google since 2011) compares two disassemblies and aligns their functions and basic blocks with graph-matching algorithms, producing per-function matches, change classification, and an overall similarity score. Classic uses: diff a vendor patch against the vulnerable binary to isolate the security fix, compare malware variants to track family evolution, or port function names and comments from an old disassembly to a new one. BinDiff has no disassembler of its own — it consumes exports from IDA Pro, Binary Ninja, or Ghidra via the BinExport plugin and works on those .BinExport files. Licensing, stated plainly: through version 6 (which is what this distro recipe packages, fetched as a prebuilt .deb) it shipped as free-but-closed-source software under a restrictive EULA; since version 8 (2024) the entire project is open source under the Apache license at google/bindiff. Both a GUI and a command-line differ are provided.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Diff two exported binaries headlessly

```
bindiff --primary=target_v1.BinExport --secondary=target_v2.BinExport --output_dir=diffs
```

Outcome: runs the full matching pipeline, writes a .BinDiff results file into diffs/ describing matched and changed functions and call-graph alignments, and prints an overall similarity score; open the results file in the GUI or IDA to walk the differences function by function.

### Batch-diff a whole directory of exports

```
bindiff /path/to/exports
```

Outcome: diffs every file in the directory against the others pairwise (IDA databases found there are exported first), producing one results file per pair — a quick way to triage a family of related samples instead of diffing pairs by hand.

### Launch the graphical comparison UI

```
bindiff --ui
```

Outcome: opens the BinDiff UI, where you select primary and secondary files interactively and inspect flow-graph and call-graph differences side by side; use this when matches need visual review rather than scripting.
