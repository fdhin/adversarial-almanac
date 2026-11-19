---
name: "hexorbase"
categories:
  - blackarch-fuzzer
version: "7"
homepage: "https://github.com/savio-code/hexorbase"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-15"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "savio-code/hexorbase"
github-stars: 76
github-pushed: "2017-09-07"
github-archived: true
github-issues: 8
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hexorbase

> PyQt4 GUI for centralized administration, SQL querying and brute-force auditing of MySQL, SQLite, Microsoft SQL Server, Oracle and PostgreSQL servers.

## What it's for

HexorBase is a database application (from the code.google.com era, by the author of Fern Wifi Cracker) for administering and auditing multiple database servers simultaneously from one window: register many connections, issue SQL queries against them, and run brute-force attacks against database accounts. It can route its connections through proxies or pivots (e.g. Metasploit route-ups) to reach servers hidden on internal subnets. Everything happens in the graphical interface — the packaged `hexorbase` command is only a launcher (it execs `execute.py` under python2) and takes no meaningful command-line arguments. The project is unmaintained: it depends on the python2/PyQt4 stack and runs only on legacy environments.

## Typical use cases

### Launch the GUI

Start the application.

```
hexorbase
```

Outcome: opens the HexorBase window; from there connections are added, SQL queries are run, and brute-force audits are configured and started through the interface — there are no flags to script.

*No corpus evidence — derived from upstream documentation.*
