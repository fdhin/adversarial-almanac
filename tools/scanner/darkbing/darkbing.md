---
name: "darkbing"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://packetstormsecurity.com/files/111510/darkBing-SQL-Scanner.1.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/darkBing"
github-stars: 0
github-pushed: "2019-03-10"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# darkbing

> Dork-driven SQLi triage: mines Bing search results for a dork, appends a quote to every URL, and flags responses matching known database-error signatures.

*No corpus evidence — derived from upstream documentation (darkBing v0.1 by baltazar a.k.a. b4ltazar, 2012; source read from the packaged script — Python 2, interactive search of Bing result pages).*

## What it's for

darkBing (baltazar a.k.a. b4ltazar, of the old darkc0de/ljuska scene) automates the "search a dork, poke each result" loop for error-based SQL injection. It runs a Bing search for your dork across the number of result pages you choose, collects the URLs, then requests each one with a single quote (`'`) appended and matches the response against a built-in dictionary of database error signatures — MySQL syntax errors, `mysql_fetch`, `ORA-01756`, JDBC/ColdFusion driver errors and more — printing `[!] w00t,w00t!` with the detected error type on a match. It only detects error-based exposure; it neither exploits nor confirms, so hits need manual verification. Python 2, threaded, with random user-agent rotation per request; output is stdout only.

## Typical use cases

### Sweep a dork's Bing results for SQL error exposure

```
darkbing -d 'index.php?id=' -c 100 -t 20
```

Outcome: `-d` is the search dork, `-c` the number of result links to collect and `-t` the thread count. The tool prints its collected-URL count, probes each URL with a trailing quote, and for every signature match prints `[!] w00t,w00t!: <url>' Error: <db type> ---> SQL Injection Found`, ending with a count of vulnerable URLs — the manual-verification worklist.
