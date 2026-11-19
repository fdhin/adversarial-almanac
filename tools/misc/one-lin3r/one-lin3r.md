---
name: "one-lin3r"
categories:
  - blackarch-misc
version: "66.711a040"
homepage: "https://github.com/D4Vinci/One-Lin3r"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: GPL-1.0-or-later
meta-checked: "2026-11-09"
github-repo: "D4Vinci/One-Lin3r"
github-stars: 1798
github-pushed: "2025-12-09"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# one-lin3r

> Searchable, scriptable database of ready-made one-liners — reverse shells, bind shells, droppers, and privilege-escalation helpers — for penetration testing.

## What it's for

Instead of remembering or re-searching shell one-liners, One-Lin3r (by D4Vinci) packages 176+ of them as a light-weight Python framework with a typo-correcting, fuzzy auto-complete console. Liners are grouped by function — Reverse Shell, Bind Shell, Dropper (download-and-execute for various payload types), and PrivEsc enumeration — for Linux, Windows, macOS, and BSD. You search by name, author, function, or content; fill context variables with `set`; use a liner interactively or copy it straight to the clipboard; and drive the whole thing non-interactively through `-x` commands and resource files. The database is extensible: new liners are Python modules dropped into the liners directory and picked up with `reload`.

## Typical use cases

### Start the console and find a liner

Locate the right one-liner for the job.

```
one-lin3r
search shell
```

Outcome: opens the interactive framework (add `-q` to skip the banner); `search` lists matching liners with name, author, function, and description, and the console's auto-complete fixes typos in typed commands (typing `seach` then TAB becomes `search`).

### Use a liner interactively

Render a one-liner with its variables filled in.

```
use linux/bash/list_all_capabilities
```

Outcome: resolves the liner and presents it for use; if it needs context values, set them first with `set <variable> <value>` (check with `variables`, autocomplete after `set` is supported), and `info <liner>` shows a liner's metadata before running it.

### Copy a liner straight to the clipboard

Skip the manual select-and-copy step.

```
copy linux/bash/list_all_capabilities
```

Outcome: resolves the liner and puts the finished one-liner on the clipboard so it can be pasted directly into a shell on the target.

### Run commands non-interactively

Script the framework from a shell or CI.

```
one-lin3r -q -x 'search dropper'
```

Outcome: executes the console command directly at startup without the banner and exits; multiple commands can be chained by separating them with semicolons.

### Replay a saved command sequence

Automate a repeatable search/copy routine.

```
one-lin3r -r resource.rc
```

Outcome: executes the commands stored in resource.rc at startup; `makerc` inside the console writes such a file from the current session's important commands, and `resource <file>` loads one without restarting.

*No corpus evidence — derived from upstream documentation.*
