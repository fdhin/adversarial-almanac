---
name: "halcyon-ide"
categories:
  - blackarch-misc
version: "2.0.2"
homepage: "https://halcyon-ide.org/"
packaged-by: [blackarch]
blackarch-builddate: "2021-01-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "s4n7h0/Halcyon-IDE"
github-stars: 359
github-pushed: "2020-08-08"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# halcyon-ide

> Graphical IDE purpose-built for writing and testing Nmap NSE scripts.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Halcyon IDE (by Sanoop Thomas) is a Java desktop application billed as the first IDE developed exclusively for Nmap Scripting Engine work. It wraps the NSE workflow in a GUI: syntax-highlighted Lua editing, a side panel for navigating the Nmap `scripts`, `nselib` and data directories, scan configuration (scan type, script arguments, packet tracing, debugging) without hand-writing command lines, and simplified post-development actions such as exporting a finished script into the Nmap installation directory and updating the script database. It is a development environment, not a scanner backend — it drives the `nmap` installed on the host. Being a Java application it needs a Java runtime; distributions ship a thin launcher that runs the jar.

## Typical use cases

### Start the IDE

Launch the graphical environment for NSE development.

```
halcyon-ide
```

Outcome: opens the Halcyon IDE window (the packaged launcher executes the jar under the system Java runtime; pass no arguments — everything else happens in the GUI). From here you create or open an NSE project, edit scripts with highlighting, run test scans from the scan-configuration panel, and export finished scripts into the local Nmap scripts directory with a script-database refresh.
