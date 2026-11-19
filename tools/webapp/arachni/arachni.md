---
name: "arachni"
categories:
  - blackarch-webapp
version: "1.6.1.3.5.g9cd73f254"
homepage: "https://www.arachni-scanner.com"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-02"
build-system: scripts
license: APACHE
aliases: ["arachni-scanner"]
meta-checked: "2026-11-09"
github-repo: "Arachni/arachni"
github-stars: 4040
github-pushed: "2026-04-22"
github-archived: true
github-issues: 131
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# arachni

> Modular Ruby web-application security scanner with a real browser environment, plus CLI, reporter, RPC and REST interfaces.

## What it's for

Arachni is a feature-full scanner framework: it crawls a web application (with an integrated browser environment for DOM/JavaScript), fingerprints the platform, audits links, forms, cookies, headers, XML and JSON inputs against a large check library (SQL/noSQL injection, XSS, XXE, code execution, ...), and saves results as `.afr` report files convertible to HTML and other formats. Reach for it when you want automated, scriptable web-app scanning; its many interfaces (packaged with dashes: `arachni-reporter`, `arachni-console`, `arachni-restore`, `arachni-rpcd`, `arachni-rest-server`) also support distributed and API-driven scanning. Note that upstream has declared the project obsolete in favor of its next-generation successor, so treat it as a legacy scanner.

## Typical use cases

### Run a full scan of a site

```
arachni http://target.example.com
```

Outcome: crawls and audits the target with all default checks; identified issues print to the terminal as they are confirmed.

### Scope the scan and save a report snapshot

```
arachni --output-verbose --scope-include-subdomains http://target.example.com --report-save-path=target.afr
```

Outcome: a verbose scan that includes subdomains in scope, with the full results saved to `target.afr` (Arachni Framework Report) for later conversion or resumption.

### Convert a saved report

```
arachni-reporter target.afr --reporter=html:outfile=my_report.html.zip
arachni-reporter --reporters-list
```

Outcome: generates a zipped HTML report from the `.afr` snapshot; `--reporters-list` prints the available output formats.

### Select or exclude checks

```
arachni http://target.example.com --checks=xss*
arachni http://target.example.com --checks=*,-csrf
```

Outcome: only the matching checks run (glob patterns, `-` to exclude), giving faster targeted audits or noise reduction on well-known finding types.

### Drive scans over the REST API

```
arachni-rest-server --address 127.0.0.1 --port 7331
```

Outcome: a scan-management REST service listens on port 7331 (the default), accepting scan submissions and progress/result queries for automation and orchestration.

No corpus evidence — derived from upstream documentation (README and the upstream wiki's user guide; commands adapted to the packaged dash-named executables).
