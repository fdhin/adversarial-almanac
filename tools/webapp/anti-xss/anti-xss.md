---
name: "anti-xss"
categories:
  - blackarch-webapp
version: "v0.1.alpha.r69.g2725dc9"
homepage: "https://github.com/lewangbtcc/anti-XSS"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "lewangbtcc/anti-XSS"
github-stars: 4
github-pushed: "2018-11-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# anti-xss

> Python 2 XSS auditor that crawls a site, mines its scripts and parameters, and writes a scan report.

## What it's for

anti-XSS automates cross-site-scripting detection: given a start URL it crawls links up to a configurable depth, collects the pages' scripts and input parameters, probes them with its detection engine, and generates a scan report (PDF via reportlab) automatically. It suits a quick automated pass over a small site. It is a 2016-era Python 2 project whose entry point exposes exactly two options — target URL and crawl depth — with no proxy, scope or profile support.

## Typical use cases

### Scan a site for XSS

```
anti-xss -u http://target.example.com/ -d 3
```

Outcome: crawls from the given URL to depth 3 (default 2), audits the discovered scripts and parameters for reflected XSS, prints findings as it goes, and generates the scan report automatically at the end.

No corpus evidence — derived from upstream documentation (README plus the packaged entry-point script's options).
