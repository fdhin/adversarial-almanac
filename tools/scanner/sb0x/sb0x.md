---
name: "sb0x"
categories:
  - blackarch-scanner
version: "2.0.1rc3.r2.gd863070"
homepage: "https://github.com/lostcitizen/sb0x-project"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-13"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "lostcitizen/sb0x-project"
github-stars: 1
github-pushed: "2014-05-28"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sb0x

> Lightweight interactive Python framework whose `load` prompt dispatches penetration-testing modules.

## What it's for

sb0x-project is a small console framework: launch it and you get a shell with `help`/`load`/`exit` (TAB completion works) where modules — for example `admin_finder` — are selected by name from the load prompt and run. It ships as a Python 2 script with a plugin API for writing your own modules (documented in the project wiki), and is a thin aggregator/dispatcher rather than a tool with built-in attack logic. Old project (2.0.x era): expect Python 2 dependencies and dated modules.

## Typical use cases

### Load and run a module

Pick an attack module from the framework shell.

```
sb0x.py
load
list
admin_finder
```

Outcome: the framework shell starts; `load` opens the load prompt, `list` enumerates the available modules, and typing a module's name (here `admin_finder`) selects and runs it; `help` or `?` prints the command reference and TAB completes names.

*No corpus evidence — derived from upstream documentation (lostcitizen/sb0x-project README; upstream entry point `sb0x.py`, module loading per the README's example).*
