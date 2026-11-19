---
name: "webanalyze"
categories:
  - blackarch-webapp
version: "131.1c20ce2"
homepage: "https://github.com/rverton/webanalyze"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-09-28"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "rverton/webanalyze"
github-stars: 1189
github-pushed: "2026-09-19"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---

# webanalyze

> Go port of Wappalyzer that fingerprints web technologies on single hosts or huge host lists.

## What it's for

Webanalyze identifies the technology stack behind a website — CMS, framework, web server, JS library, CDN — by matching responses against a fingerprint database (`technologies.json`). It was built to be performant on large lists of hosts, with configurable worker counts, optional link crawling from the root page, and stdout, CSV, or JSON output. Since Wappalyzer withdrew public access to its application definitions, `-update` pulls the database from the enthec/webappanalyzer community fork. It identifies technologies from fingerprints; it does not version-audit or scan for vulnerabilities itself. The package can also be used as a Go library (see `cmd/webanalyze/main.go`).

## Typical use cases

### Refresh the fingerprint database

Load current technology definitions before scanning.

```
webanalyze -update
```

Outcome: replaces the local `technologies.json` with the current definitions from the enthec/webappanalyzer project; scans use this file by default.

### Fingerprint one host with light crawling

See what a single site runs, beyond its homepage.

```
webanalyze -host example.com -crawl 1
```

Outcome: fingerprints the root page and follows one level of links, printing per-URL findings grouped by category with versions where detectable (e.g. `Hugo, 0.42.1 (Static Site Generator)`).

### Scan a list of hosts at scale

Run detection across a whole scope file.

```
webanalyze -hosts hosts.txt -worker 16 -output csv
```

Outcome: tests every host in the file (one per line) with 16 concurrent workers and emits `Host,Category,App,Version` CSV rows on stdout; `-output json` emits machine-readable records instead.

### Send custom headers

Reach targets that need specific headers.

```
webanalyze -host example.com -header "User-Agent: Mozilla/5.0" -header "Authorization: Bearer ***"
```

Outcome: each `-header` value (repeatable) is attached to every request the fingerprinter makes.

*No corpus evidence — derived from upstream documentation (rverton/webanalyze README).*
