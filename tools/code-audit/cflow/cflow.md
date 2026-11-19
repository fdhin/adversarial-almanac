---
name: "cflow"
categories:
  - blackarch-code-audit
version: "1.8"
homepage: "http://www.gnu.org/software/cflow/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-05-04"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "gitGNU/gnu_cflow"
github-stars: 0
github-pushed: "2017-05-02"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cflow

> GNU cflow: prints call graphs and symbol cross-references from C source code.

## What it's for

GNU cflow analyzes C source files and prints a flow graph — an indented tree rooted at `main()` that shows every function it calls, recursively, with signatures and definition sites (file:line). A reverse mode inverts the relation to show who calls a given function, and a cross-reference mode lists where each symbol appears. In code audit it answers structural questions without running anything: which paths reach a dangerous function, how deep a call chain is, or which call sites a fix has to satisfy. It parses C (with an optional preprocessor pass); it has no vulnerability database and executes nothing — it purely maps structure. This is GNU cflow (gnu.org/software/cflow); it shares nothing with unrelated same-named projects on GitHub.

## Typical use cases

### Print the call graph of a program

```
cflow main.c util.c
```

Outcome: an indented tree starting at `main()`; each defined function shows its signature and location in angle brackets, nesting encodes the call chain, and library calls such as `printf` appear as leaf entries.

### Show who calls a function (reverse graph)

```
cflow --reverse util.c
```

Outcome: output inverted per symbol — each function heading lists its callers — so the path from any helper back to `main()` is a single vertical scan. Add `--brief` to collapse repeated entries produced by recursion.

### Include hidden symbols in the graph

```
cflow -i _ parser.c
```

Outcome: symbols normally omitted — names beginning with an underscore — appear in the output. The `-i` spec also accepts `s` (static symbols), `x` (data symbols), and `^` to exclude the classes that follow, giving fine control over what the graph shows.

### Root the graph at a chosen function

```
cflow -m parse_request router.c
```

Outcome: the tree starts at `parse_request` instead of `main()` (`-m`/`--main`), so one subsystem can be mapped in isolation without tracing from the entry point.

### Cross-reference every symbol

```
cflow -x *.c
```

Outcome: instead of a graph, one line per symbol occurrence showing its identifier and source location, with definitions marked — a quick where-used index across the codebase.

*No corpus evidence — derived from upstream documentation.*

GitHub: gitGNU/gnu_cflow is a third-party mirror of the Savannah (sv.gnu.org) cflow git repository (last synced 2017, v1.5-era; the packaged release is 1.8).
