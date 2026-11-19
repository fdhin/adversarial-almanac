---
name: "bbscan"
categories:
  - blackarch-webapp
version: "53.29b9f11"
homepage: "https://github.com/lijiejie/bbscan"
packaged-by: [blackarch]
blackarch-builddate: "2025-01-05"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "lijiejie/bbscan"
github-stars: 2370
github-pushed: "2024-12-31"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bbscan

> Fast, lightweight batch scanner that triages large lists of web targets for common weaknesses.

## What it's for

BBScan is a high-concurrency web vulnerability scanner meant to sift a large number of hosts and quickly surface the ones worth manual attention. It runs a rule set over each target — information leaks (git/svn exposure, backup files), directory traversal, admin backends — and additionally parses JavaScript to extract API endpoints and leaked keys/tokens, plus web fingerprinting to identify frameworks, languages and CMS products. It is a triage tool, not an exploiter: the output is a report of possibly-vulnerable targets to feed into semi-automated testing. Current upstream requires Python 3.6+.

## Typical use cases

### Scan a batch of targets from a file

Feed BBScan a newline-delimited list of URLs.

```
bbscan -f urls.txt --api
```

Outcome: scans every target in `urls.txt` and writes an HTML report per batch; `--api` additionally gathers and displays all API endpoints extracted from each site's `.js` files.

### Scan hosts given on the command line

Skip the target file when testing a handful of hosts.

```
bbscan --host target.example.com https://target2.example.com http://target3.example.com:8080 198.51.100.10
```

Outcome: runs the full rule set against each host or URL given inline and reports findings when the scan completes.

### Expand each target to its network neighbours

Sweep the CIDR around each target instead of single hosts.

```
bbscan -f urls.txt --network 24
```

Outcome: for every target in the file, also scans all live IPs in the surrounding /24 (mask must be between 8 and 31) and includes them in the report.

### Restrict the scan to named rules

Limit the run to specific checks instead of the whole rule set.

```
bbscan --rule git_and_svn -f urls.txt
```

Outcome: only the named rules are executed across the target list, which makes large sweeps much faster; combine with `--fp` for fingerprint-only mode.

No corpus evidence — derived from upstream documentation.
