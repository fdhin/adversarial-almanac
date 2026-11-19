---
name: "vivisect"
categories:
  - blackarch-debugger
version: "v1.3.2.r10.g66c93f0c"
homepage: "https://github.com/vivisect/vivisect"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-09-06"
build-system: python
license: BSD
meta-checked: "2026-11-09"
github-repo: "vivisect/vivisect"
github-stars: 1002
github-pushed: "2026-10-21"
github-archived: false
github-issues: 127
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 18
gh-activity-checked: "2026-11-09"
---

# vivisect

> Python static-analysis and reverse-engineering framework (Vivisect/VDB/Vtrace): parse and analyze PE/ELF/etc. binaries into a queryable workspace, with a GUI, a scriptable API, and an attached debugger.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Vivisect is a pure-Python reverse-engineering stack that loads a binary (PE, ELF, Mach-O, and more), runs automatic analysis over it, and stores the results — function boundaries, code blocks, cross-references, strings, imported symbols, structure parses — in a workspace object you can then query from Python, browse in its PyQt GUI, or share with others. The same project ships VDB, a debugger built on the Vtrace trace library, and vstruct for declarative binary-structure parsing. Reach for it when you want programmatic analysis of many binaries (triage pipelines, YARA-style hunting over disassembly) or a collaborative multi-analyst workspace; the workspace serialises to a `.viv` file that keeps all analysis results, so the expensive pass runs once. Since 1.0.0 it is Python 3 only. It is not a hex editor or an interactive patcher by design — its strength is the API surface over the analyzed program.

## Typical use cases

### Open a binary in the GUI with automatic analysis

```
vivbin /path/to/binary.exe
```

Outcome: kicks off auto-analysis (function finding, xrefs, strings, imports) and opens the Vivisect UI on the results. `vivbin` with no arguments opens an empty workspace; the same entry point is available as `python -m vivisect.vivbin`.

### Run bulk analysis headless and keep the workspace

```
vivbin -B /path/to/binary.exe
```

Outcome: runs the full auto-analysis without a GUI and writes the saved workspace to `/path/to/binary.exe.viv`. Reopen it later (in the GUI or from scripts) with `vivbin /path/to/binary.exe.viv` — no re-analysis needed.

### Analyze a binary programmatically

```python
import vivisect

vw = vivisect.VivWorkspace()
vw.loadFromFile("/path/to/binary")
vw.analyze()
```

Outcome: after `analyze()` returns, `vw` is populated with the analysis results — iterate function boundaries, locations, and cross-references (`vw.getXrefs()`), or drive emulation and deeper analysis modules from there. `loadFromFd` accepts an already-opened file object instead of a path.

### Attach the VDB debugger to a running process

```
vdbbin -p <pid>
vdbbin -Q
```

Outcome: VDB attaches to the given process id (or opens its console UI — `-Q` — for an already-traced target) giving an interactive debugger prompt driven by the same Vtrace engine that Vivisect uses for analysis.

### Serve workspaces for collaborative reversing

```
vivserver /path/to/top/directory
```

Outcome: starts a Vivisect server sharing every workspace file under that directory over the network; other Vivisect instances connect (from the GUI's Share menu or `File -> Save to Server`) and edits propagate between analysts in real time.
