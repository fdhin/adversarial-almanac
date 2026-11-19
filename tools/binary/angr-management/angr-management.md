---
name: "angr-management"
categories:
  - blackarch-binary
version: "9.2.179"
homepage: "https://github.com/angr/angr-management"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: BSD-2-Clause
meta-checked: "2026-11-09"
github-repo: "angr/angr-management"
github-stars: 1203
github-pushed: "2026-11-07"
github-archived: false
github-issues: 246
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 291
gh-activity-checked: "2026-11-09"
---

# angr-management

> Cross-platform GUI for the angr binary analysis platform.

## What it's for

angr-management is the official graphical front-end to angr: load a binary and get interactive disassembly and decompilation views, run angr's analyses from the UI, and inspect results visually. It is a PySide6 desktop application (Python 3.12+) installed under the command names `angr-management` and `am`. Beyond the GUI it can run Python scripts headlessly in the angr environment, act as a daemon that handles `angr://` URLs, and expose an MCP server so AI agents can drive analyses and show the results in the GUI.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Open a binary in the GUI

Jump straight from the shell into visual analysis.

```
angr-management ./target
```

Outcome: the main window opens with the binary already loaded; without a path argument a welcome/open dialog appears instead.

### Run a script headlessly in the angr environment

Automate analysis without starting the Qt GUI.

```
angr-management -n -s analyze.py
```

Outcome: executes `analyze.py` inside the angr environment with no GUI; add `-i` to drop into an IPython session afterwards with the script's globals available as `script_globals`.

### Expose the GUI to an AI agent over MCP

Let an MCP-capable agent client run analyses and render them in the interface.

```
angr-management -m --mcp-port 8642 ./target
```

Outcome: the GUI starts with an MCP server listening on port 8642 (the default); agents authenticate with the configured bearer token (`--mcp-auth`, `--mcp-auth-token`) and their analysis results appear in the loaded workspace.
