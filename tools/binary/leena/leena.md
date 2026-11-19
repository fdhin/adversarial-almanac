---
name: "leena"
categories:
  - blackarch-binary
version: "2.5119f56"
homepage: "https://github.com/mmicu/leena"
packaged-by: [blackarch]
blackarch-builddate: "2018-10-03"
build-system: node
license: GPL3
meta-checked: "2026-11-09"
github-repo: "mmicu/leena"
github-stars: 13
github-pushed: "2016-04-05"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# leena

> Symbolic execution of browser JavaScript functions, combining Chrome's debugging protocol with an SMT solver.

## What it's for

leena is a research prototype (archived 2016) that tries to explore all
branches of JavaScript functions: it executes the code concretely inside
Chrome, driven through the Chrome debugging protocol, and solves branch
conditions symbolically with an SMT solver (z3 by default, cvc4 supported).
You describe the JS files under test in a JSON config (server ports, the
Chrome debugging endpoint, solver paths), start Chrome with debugging
enabled, and leena instruments the files, exercises every global function,
and summarizes the exploration in an HTML report. It targets client-side
browser code — not Node.js programs or native binaries — and given its age
should be treated as a historical artifact of JavaScript symbolic
execution.

## Typical use cases

### Explore every branch of a configured web app

```
node bin/leena examples/browser/1/leena_config.json
```

Outcome: leena instruments the files listed in the config's `files`
property, drives their global functions through Chrome, solves each branch
condition, and writes an HTML report of the exploration results.

### Switch the SMT solver

```
node bin/leena leena_config.json -s cvc4
```

Outcome: branch constraints are handed to cvc4 instead of the default z3;
the chosen solver must be listed in the config's `smt-solvers` map.

### Show the CLI surface

```
node bin/leena --help
```

Outcome: prints usage — a config file is the only positional command,
supplemented by `--no-color` and `-s/--smt-solver`.

*No corpus evidence — derived from upstream documentation.*
