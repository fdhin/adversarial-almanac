---
name: "angr"
categories:
  - blackarch-binary
version: "9.2.194"
homepage: "https://pypi.org/project/angr/#files"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-22"
build-system: python
license: BSD-2-Clause
aliases: ["angr-py2"]
meta-checked: "2026-11-09"
github-repo: "angr/angr"
github-stars: 9337
github-pushed: "2026-11-07"
github-archived: false
github-issues: 803
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1507
gh-activity-checked: "2026-11-09"
---

# angr

> Platform-agnostic Python framework for symbolic execution and binary analysis.

## What it's for

angr is a suite of Python 3 libraries for analyzing binaries: loading many executable formats and CPU architectures, disassembly and intermediate-representation lifting, program instrumentation, symbolic execution, control-flow and data-dependency analysis, value-set analysis and decompilation. There is no angr CLI to speak of — you drive it from Python scripts or a REPL, typically starting from an `angr.Project` bound to the target binary; the angr-management GUI wraps the same engine for interactive work. Typical jobs range from solving input constraints in license checks and crackmes to vulnerability research and firmware analysis.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Load a binary and inspect its entry point

The starting point for every analysis.

```
python3 - <<'PY'
import angr
proj = angr.Project("./target", auto_load_libs=False)
print(proj.entry)
PY
```

Outcome: loads the binary with shared libraries left unloaded and prints the entry-point address; in IPython, tab-completion on `proj` walks the top-level-accessible API.

### Solve for input that reaches a target address

Classic find/avoid symbolic execution.

```
python3 - <<'PY'
import angr
proj = angr.Project("./target", auto_load_libs=False)
simgr = proj.factory.simgr()
simgr.explore(find=0x400844, avoid=0x400850)
print(simgr.found[0].posix.dumps(0))
PY
```

Outcome: explores the state space, steering away from the `avoid` address and stopping at `find`; the input bytes that reach the target print from the first state in the `found` stash (check that the stash is non-empty before indexing in real scripts).

### Reach a state by observable output

Stop on a success message instead of a hardcoded address.

```
python3 - <<'PY'
import angr
proj = angr.Project("./target", auto_load_libs=False)
simgr = proj.factory.simgr()
simgr.explore(find=lambda s: b"Success" in s.posix.dumps(1))
print(simgr.found[0].posix.dumps(0))
PY
```

Outcome: `find` also accepts a predicate on the state — here execution halts when stdout contains the marker string, and the stdin that produced it is dumped.

### Recover the control-flow graph

Map functions before diving into them.

```
python3 - <<'PY'
import angr
proj = angr.Project("./target", auto_load_libs=False)
cfg = proj.analyses.CFGFast()
print(len(cfg.model.graph.nodes()), "nodes")
print(cfg.kb.functions[proj.entry].name)
PY
```

Outcome: statically recovers the CFG and function boundaries; node and edge counts read from the NetworkX graph, and `cfg.kb.functions` exposes per-function names, blocks and call sites.
