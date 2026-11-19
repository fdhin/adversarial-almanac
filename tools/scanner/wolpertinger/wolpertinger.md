---
name: "wolpertinger"
categories:
  - blackarch-scanner
version: "2.58ef8e2"
homepage: "https://github.com/Crapworks/wolpertinger"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-24"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Crapworks/wolpertinger"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# wolpertinger

> A distributed port scanner from the Crapworks collective — autotools C project, gone from GitHub, preserved only in the BlackArch package.

## What it's for

Wolpertinger was a distributed port scanner: instead of one host sweeping a range, scanner nodes shared the work and pooled results. The packaged source is an autotools C project (`configure`/`Makefile.am`, `src/`, `docs/`, companion Python 2 scripts) whose dependencies — libpcap for packet handling, libdnet for crafting, SQLite for storing scan results — tell you the design: raw-packet scanning with results collected in a database. The upstream repository (Crapworks/wolpertinger, "distributed portscanner") has vanished from GitHub; an archived snapshot of the repo page from 2022 confirms only the one-line description and the file tree, and its README was never captured by the Wayback Machine. No verifiable command-line interface survives from an authoritative source, so this note deliberately documents no example invocations — treat the package as a historical artifact and expect to read `docs/` from the BlackArch source package before attempting a build.

*No corpus evidence — no verifiable CLI upstream; description-only note (upstream repo gone, README not archived).*
