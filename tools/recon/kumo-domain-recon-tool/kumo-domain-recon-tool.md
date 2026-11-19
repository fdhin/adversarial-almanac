---
name: "kumo-domain-recon-tool"
categories:
  - recon
version: "git (2026-11-05)"
homepage: "https://github.com/karim852/kumo-domain-recon-tool"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "karim852/kumo-domain-recon-tool"
github-stars: 111
github-pushed: "2026-11-05"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 72
gh-activity-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 1
---

# kumo-domain-recon-tool

> Kumo (蜘蛛, "spider") — an interactive domain OSINT framework: one domain in, 29 parallel modules out (DNS, CT-log subdomains, ports, fingerprints, leaked credentials), plus a built-in vulnerability scanner and web dashboard.

## What it's for

All-in-one external recon of a domain you are allowed to test: drop a domain on the CLI (or the interactive home screen) and Kumo fans out 29 modules in parallel across network/infrastructure, web-application, and threat-intelligence groups — DNS records, certificate-transparency subdomains, port scanning, technology fingerprints, leaked-credential lookups, Wayback crawling, e-mail harvesting, cloud-bucket discovery and more. A built-in detection-only vulnerability scanner (419 checks, including fingerprints for recent exploited CVEs) correlates findings without running exploits, and results export to JSON or a Flask web dashboard. Needs no root and no API keys (a few modules use optional keys read from the environment at run time). It is an orchestrator over public data sources — it does not attack anything, and its vuln checks detect/correlate rather than exploit.

## Typical use cases

### Run a full scan of a domain

```
python3 kumo.py example.com
```

Outcome: launches all 29 modules in parallel against the domain and streams results to the terminal (a spider animation tracks modules still in flight); each module's findings print grouped by section. With no argument the same command opens the interactive home screen where you select modules by number (`a` for all, `f` for fast) and enter the domain when prompted.

### Skip the slow modules or pick specific ones

```
python3 kumo.py example.com --fast
python3 kumo.py example.com -m dns ssl ports
```

Outcome: `--fast` runs the scan without the time-heavy modules (wayback, brute-force, subdomain enumeration, screenshots, e-mail harvest, nuclei checks, cloud buckets); `-m` runs only the named modules — the quick iteration loop while scoping before committing to a full pass.

### Export results for reporting

```
python3 kumo.py example.com -o report.json
```

Outcome: writes the aggregated findings of the run to `report.json`, suitable for diffing between scheduled scans or feeding a report pipeline. Add `--no-color` for pipe-friendly terminal output and `--no-spider` to disable the crawl animation when logging to a file.

### Browse results in the web dashboard

```
python3 kumo.py --web -p 9000
```

Outcome: serves Kumo's Flask dashboard on port 9000 (default 8888, `--host` to change the bind address) where module results are browsable in a UI — useful while iterating on a large domain. Run a scan and the dashboard side by side: findings appear as each module lands.

### See what the framework covers

```
python3 kumo.py --list-modules
```

Outcome: prints every module grouped by category with a one-line description of what each returns — the inventory to consult before narrowing with `-m`, and the quick answer to "does Kumo check X?".

*No corpus evidence — derived from upstream documentation.*
