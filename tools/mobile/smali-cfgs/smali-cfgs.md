---
name: "smali-cfgs"
categories:
  - blackarch-mobile
version: "6.4450418"
homepage: "https://github.com/ch0psticks/Smali-CFGs"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "ch0psticks/Smali-CFGs"
github-stars: 4
github-pushed: "2013-12-29"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# smali-cfgs

> Build control-flow graphs for smali classes, methods and cross-references.

## What it's for

Smali-CFGs renders control flow graphs from disassembled Dalvik code — the
smali files produced by tools like apktool. Two scripts do the work: one
builds the CFG of a single method or of a whole class file, the other builds
cross-reference graphs showing which classes call which methods. Graphs are
emitted through pydot/Graphviz, giving a visual map of branchy or obfuscated
methods that is easier to reason about than raw smali text. The project dates
from 2013 and its packaging is Python 2 era (BlackArch installs it with
`python2-pydot`), so treat it as a small legacy utility: it visualizes
control flow, it does not decompile, search or score anything.

## Typical use cases

### Draw the CFG of one method

See the branch structure of a specific method.

```
smali-cfgs-flow -c smali/com/example/CoreService.smali -m run
```

Outcome: renders the control flow graph of the method named `run` inside the
given smali class file and writes the graph image next to the source (the
upstream README invokes the same script as `flow.py`).

### Draw the CFG of a whole class

Get every method in a class on one graph.

```
smali-cfgs-flow -c smali/com/example/CoreService.smali
```

Outcome: without `-m`, all methods of the class are laid out into a single
class-level control flow graph, useful for spotting shared helpers and
dispatcher patterns within one file.

### Map cross-class method calls

Follow who calls a method across the whole smali tree.

```
smali-cfgs-xref -d smali/ -m XRefBoth -f "com/example/a/g->a"
```

Outcome: scans the smali directory (`-d`) for references to the method
specified as `class->method` (`-f`) and draws the caller/callee graph —
`-m XRefBoth` includes both incoming and outgoing edges (upstream name:
`xref.py`).

*No corpus evidence — derived from upstream documentation.*
