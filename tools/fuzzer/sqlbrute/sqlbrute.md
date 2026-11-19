---
name: "sqlbrute"
categories:
  - blackarch-fuzzer
version: "1.0"
homepage: "http://www.justinclarke.com/archives/2006/03/sqlbrute.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "GDSSecurity/SQLBrute"
github-stars: 73
github-pushed: "2020-01-21"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sqlbrute

> SQLBrute — extracts database contents through blind SQL injection by brute-forcing each character (error-based or time-based).

*No corpus evidence — derived from upstream documentation.*

## What it's for

SQLBrute (Justin Clarke, GDS Security, 2006) is a multi-threaded Python brute-forcer for blind SQL injection: where an injection point produces an observable difference — an error page matched by a regex, or a delay via MS SQL Server `WAITFOR DELAY` — it enumerates database, table and column contents character by character over a default charset covering letters, digits and common symbols (`-`, `.`, `_`, `+`, `#`, `@`, `$`). It targets MS SQL Server (default) and Oracle, sends GET or POST (`--data`), carries cookies and extra headers, restricts rows with a `--where` clause, and writes recovered values to stdout or a file. One URL and one injection point per run.

## Typical use cases

### Extract data with error-page matching

Uses a regex that distinguishes a hit from a miss.

```
sqlbrute --data "searchtype=state&state=1'" --error "NO RESULTS" --database webapp --table customer --column custnum --where password=password http://target.example.com/locator.asp
```

Outcome: appends the injection to the POST data, classifies each probe by whether the response matches the error regex, and walks out the values of `custnum` from `webapp.customer` (restricted by the where clause), printing characters as they resolve — the tool's own documented example invocation.

### Extract data time-based when pages look identical

Falls back to timing when there is no visible error difference.

```
sqlbrute --time --server sqlserver --database webapp --table customer --column custnum http://target.example.com/locator.asp
```

Outcome: each probe that matches the searched character stalls the response via `WAITFOR DELAY`, so values are inferred purely from response time — far slower than error-based mode; `--server oracle` switches to Oracle timing idioms.
