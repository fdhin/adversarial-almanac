---
name: "operative"
categories:
  - blackarch-fingerprint
version: "148.163acdf"
homepage: "https://github.com/graniet/operative-framework"
packaged-by: [blackarch]
blackarch-builddate: "2022-08-14"
build-system: go
license: Apache
aliases: ["operative-framework"]
meta-checked: "2026-11-09"
github-repo: "graniet/operative-framework"
github-stars: 756
github-pushed: "2025-05-17"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# operative

> Digital-investigation framework that runs OSINT and fingerprinting modules against websites, people and companies from an interactive console or one-shot CLI flags.

## What it's for

Operative (operative-framework) bundles reconnaissance modules — search-engine and social-network lookups, reverse email/IP whois, web-archive and header retrieval, exploit search — under a session manager that tracks targets, links between them, notes and results, and can export a PDF report. You either work interactively in its console (enter a module, `set TARGET`, `run`) or fire a single module headlessly with `-e` for scripting, with JSON/CSV output available. It is an enrichment and investigation helper, not a network scanner: modules are query-and-collect operations against named targets. The upstream repository has since been redesigned in Rust under `opf-*` crates; the packaged Go build remains the released, documented version.

## Typical use cases

### List available modules

```
operative -l
```

Outcome: prints every module the framework can run (e.g. `header_retrieval`, `linkedin_search`, `web_archive`, `searchsploit`, `get_ipaddress`) and exits — use it to discover capability before targeting anything.

### Run one module headlessly against a target

```
operative -e header_retrieval -t example.com --json
```

Outcome: executes only the `header_retrieval` module against example.com and prints its results as JSON (`--csv` for CSV) instead of opening the console; extra module parameters go in `-p`.

### Investigate a target interactively

```
operative
```

then, inside the console:

```
web_archive
set TARGET example.com
run
```

Outcome: opens the interactive session, enters the `web_archive` module, binds example.com as its TARGET and runs it — results are stored on the session's target, viewable later with `view results`; `help` lists all console commands.

### Start with the REST API exposed

```
operative -a
```

Outcome: boots the framework with its RESTful API listening immediately (the console command `info api` shows its address), so external tools can drive targets and modules over HTTP.

*No corpus evidence — derived from upstream documentation.*
