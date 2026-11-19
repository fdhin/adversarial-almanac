---
name: "sqlivulscan"
categories:
  - blackarch-scanner
version: "249.cc8e657"
homepage: "https://github.com/the-robot/sqliv"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "the-robot/sqliv"
github-stars: 1237
github-pushed: "2018-08-14"
github-archived: true
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-09"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sqlivulscan

> SQLiv — dork-driven mass SQL-injection scanning: feed it a search-engine dork and it tests every site the search returns.

## What it's for

SQLiv (packaged by BlackArch as `sqlivulscan`, entry point `sqliv.py`) is a Python 2 massive SQL-injection scanner with three modes: dork scanning (search Bing, Google or Yahoo for a dork, then scan every result), targeted scanning of one domain (crawl it, collect URLs with query parameters, scan each), and reverse-domain scanning (find other sites hosted on the target's server and scan those too). Scanning and domain lookups run in multiprocessing, so wide dork scans finish quickly, and results can be dumped as JSON. It is a web-application URL tester only — it does not exploit found injections. The upstream repository is archived (2018); its address changed from Hadesy2k/sqlivulscan to the-robot/sqliv in the meantime.

## Typical use cases

### Scan the results of a dork

```
sqlivulscan -d "inurl:index.php?id=" -e google
```

Outcome: queries Google for the dork, collects the hit sites and scans each in parallel for SQL-injection indicators, printing vulnerable URLs as they are found.

### Scan one target with crawling

```
sqlivulscan -t www.example.com
```

Outcome: crawls the site, gathers URLs containing query parameters and tests each one — the right mode when the engagement is scoped to a single application. A specific URL with parameters (`www.example.com/index.php?id=1`) is accepted directly.

### Scan every site on the target's server

```
sqlivulscan -t www.example.com -r -o result.json
```

Outcome: resolves the other domains hosted on the same server (reverse domain) and scans them as well, surfacing forgotten sister applications on shared infrastructure; `-o` dumps the full result set to `result.json` for the report.

*No corpus evidence — derived from upstream documentation.*
