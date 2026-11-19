---
name: "nsearch"
categories:
  - blackarch-misc
version: "353.bd8205b"
homepage: "https://github.com/JKO/nsearch"
packaged-by: [blackarch]
blackarch-builddate: "2020-12-04"
build-system: scripts
license: APACHE
meta-checked: "2026-11-09"
github-repo: "JKO/nsearch"
github-stars: 129
github-pushed: "2026-11-07"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 45
gh-activity-checked: "2026-11-09"
---

# nsearch

> Interactive console (NSEarch) for searching the nmap NSE script database, with favorites, scan templates, and staged scan workflows.

## What it's for

nmap ships hundreds of NSE scripts and finding the right one is its own search problem. nsearch (NSEarch, started by Jacobo Tibaquira; the packaged upstream is the actively maintained JKO fork) indexes nmap's `script.db` into a local SQLite database and offers an interactive console: search scripts by name, category, and author in one query, read a script's embedded documentation, keep a ranked favorites list, launch nmap with predefined templates, and run multi-stage workflows where each stage scans only what earlier stages found, with saved reports you can diff between runs. The database is built on first run and rebuilt only when nmap's script.db changes; favorites survive rebuilds. It does not replace nmap — it drives it.

## Typical use cases

### Search the NSE script database

Find candidate scripts before writing a `--script` argument.

```
nsearch
search name:http category:vuln
```

Outcome: the first run builds the local script database, then the console lists every matching `.nse` script with name and author, sorted by name; combine any of `name:`, `category:`, and `author:` filters, quoting values with spaces (e.g. `search author:"Paulino Calderon"`), and use `last` to redisplay results.

### Read a script's documentation

Check what a script actually does before running it.

```
doc sshv1.nse
```

Outcome: prints the script's embedded description, author, license, and categories; TAB autocompletes over the last search results and the `.nse` extension is optional.

### Keep a ranked favorites list

Track the scripts you rely on across engagements.

```
addfav name:http-vuln-cve2012-1823.nse ranking:great
showfav ranking:great
```

Outcome: records the script with ranking `great`, then lists only great-ranked favorites; `modfav name:... newranking:...` re-ranks and `delfav name:...` removes an entry.

### Launch a quick scan from a template

Run nmap without composing flags by hand.

```
run template:ping net:127.0.0.1
```

Outcome: executes nmap with the `ping` template (host discovery only) against the target and prints host state; shipped templates also cover `default` (services on ports 1-5000), `http`, and `ssh`. `run` starts immediately, so only point it at systems you are authorized to test.

### Run a staged workflow and report on it

Scan a network once and drill down automatically.

```
workflow run name:basic net:198.51.100.0/24
workflow report id:1 format:md
```

Outcome: shows the stage plan (discovery → top-1000 ports → service detection) and asks for confirmation, then stores results locally; `report` writes `reports/workflow-1.md` (`format:json` for machine-readable), `compare id:1 with:2` diffs two runs of the same target, and `resume id:1` continues an interrupted run from where it stopped.

*No corpus evidence — derived from upstream documentation.*
