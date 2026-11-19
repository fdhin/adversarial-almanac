---
name: "pown"
categories:
  - blackarch-webapp
version: "332.0e32edf"
homepage: "https://github.com/pownjs/pown"
packaged-by: [blackarch]
blackarch-builddate: "2022-12-02"
build-system: node
license: MIT
meta-checked: "2026-11-09"
github-repo: "pownjs/pown"
github-stars: 260
github-pushed: "2023-04-07"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pown

> Modular Node.js security toolkit where every capability is a standalone NPM module (`pown-*`).

## What it's for

The entry point of the Pown.js family: instead of one monolithic framework (its authors call frameworks an anti-pattern), each feature — recon, port scanning, credentials checking, exploits — lives in its own NPM-installable module composed under a single CLI. This package provides the launcher itself: module management, a scripting engine for chaining commands, an interactive shell, and self-update. Optional modules install from the NPM registry into `~/.pown/modules`, so the attack surface you carry is exactly what you installed. Reach for it when you want composable, scriptable offensive tooling in JavaScript; if you need a specific capability, install the matching `pown-*` module rather than expecting it built in.

## Typical use cases

### Update the global installation

Keep the toolkit and its launcher current.

```
pown update
```

Outcome: updates the globally installed pown packages (aliases: `upgrade`, `up`, `u`); run `pown --version` afterwards to confirm the new version.

### Install and manage optional modules

Pull extra capabilities from NPM.

```
pown modules
```

Outcome: prints the module-manager interface for adding/removing `pown-*` modules; installed optional modules land in `~/.pown/modules` and their commands become available under the same `pown` CLI alongside the builtin ones (`credits`, `preferences`, ...).

### Automate commands with the scripting engine

Chain pown commands in a script file instead of typing them.

```
pown script tasks.js
```

Outcome: executes the script through pown's simple automation engine (`script [file] [args...]`) — each line drives the same commands the CLI offers, useful for repeatable recon/check sequences.

### Use it as an interactive shell

Drive everything from a REPL-style session.

```
pown shell
```

Outcome: opens the simple pown shell, where every pown command can be run interactively with results in-session — convenient for exploratory work before freezing a sequence into a `script` file.

*Provenance: no corpus evidence — derived from upstream documentation (README usage block, verified 2026-11).*
