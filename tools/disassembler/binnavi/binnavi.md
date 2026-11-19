---
name: "binnavi"
categories:
  - blackarch-disassembler
version: "6.1.0"
homepage: "https://github.com/google/binnavi"
packaged-by: [blackarch, aur]
blackarch-builddate: "2018-09-12"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "google/binnavi"
github-stars: 2886
github-pushed: "2020-10-23"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# binnavi

> Google's binary analysis IDE for navigating and annotating control-flow and call graphs, backed by a PostgreSQL database.

## What it's for

BinNavi is a Java GUI for collaborative reverse engineering. Disassemblies are imported through its bundled IDA Pro export plugin into a central PostgreSQL database, and analysts then inspect, navigate, edit and annotate the control-flow graphs and call graphs, collect and combine execution traces, and share comments and naming across a team — the database is the shared workspace, not the local binary. Google ended development in 2020 and the repository is archived; running it requires Java 11+ and an accessible PostgreSQL instance. There is no headless CLI and no built-in disassembler engine of its own — it consumes disassemblies exported from IDA (other export plugins were community hopes that never landed).

## Typical use cases

### Launch the IDE

Start BinNavi against its database backend.

```
binnavi
```

Outcome: starts the GUI (the packaged wrapper runs `java -jar /usr/share/binnavi/binnavi-all.jar`); the first run prompts for PostgreSQL connection details, after which disassemblies, comments and traces are stored in that database and can be shared between analysts.

*No corpus evidence — derived from upstream documentation.*
