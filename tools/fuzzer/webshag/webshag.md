---
name: "webshag"
categories:
  - blackarch-fuzzer
version: "1.10"
homepage: "http://www.scrt.ch/en/attack/downloads/webshag"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "wereallfeds/webshag"
github-stars: 35
github-pushed: "2016-04-10"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webshag

> SCRT's modular web server audit suite: fingerprinting, spidering, URL scanning and file fuzzing.

## What it's for

Webshag (SCRT – Information Security, v1.10) is a Python web server auditor built from independent modules that run standalone or chained: `pscan` (port scanning), `info` (server banner and version), `spider` (crawling), `uscan` (URL scanning against nikto-derived and custom test databases, with false-positive suppression) and `fuzz` (directory and file brute forcing from bundled lists or a generated filename space). It ships both a CLI (`webshag_cli.py`) and a wxWidgets GUI (`webshag_gui.py`), configured through `webshag.conf`. The bundled test databases date from the late 2000s, so treat its findings as leads to confirm rather than authoritative results.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Update the scanning databases

Refresh the URL-scanner databases before auditing.

```
./webshag_cli.py -U
```

Outcome: fetches the newest nikto-derived and custom test databases used by the `uscan` module, reports per-database success, and exits.

### URL-scan a web server

Probe a target with the test database across several ports and roots, then export a report.

```
./webshag_cli.py -m uscan -p 80,8080 -r /,/admin/ -x -o html -f report.html target.example.com
```

Outcome: fingerprints the server, runs the test database against each port/root pair, filters false positives, and writes `report.html` summarizing every flagged finding.

### Crawl a site with the spider module

Map a site's link structure before deeper testing.

```
./webshag_cli.py -m spider -i /index.php target.example.com
```

Outcome: crawls from the given initial page and prints the discovered URL structure, which seeds further enumeration of the same host.

### Fuzz directories and files by list

Brute-force the path space with the bundled wordlists.

```
./webshag_cli.py -m fuzz -n list -e 11 -p 80 target.example.com
```

Outcome: fuzzes both directories and files (`-e 11`; use `10` for directories only, `01` for files only) from the bundled directory lists against port 80, reporting paths that return non-error responses. `-n gen` switches to a filename-generator mode driven by a `-g` expression.
