---
name: "dirscraper"
categories:
  - blackarch-webapp
version: "16.e752450"
homepage: "https://github.com/Cillian-Collins/dirscraper"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Cillian-Collins/dirscraper"
github-stars: 224
github-pushed: "2019-02-24"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dirscraper

> Discovers hidden directories and endpoints by mining a website's JavaScript files.

## What it's for

Dirscraper feeds the recon stage: it visits a domain, collects the site's script tags, reads the JavaScript source of each, and extracts the subdomains and endpoints referenced inside — paths that are never linked in HTML but remain reachable. This widens the attack surface when ordinary content discovery finds nothing. Input is a single URL; it is a parser, not a fuzzer.

## Typical use cases

### Mine a site's JavaScript

```
dirscraper -u https://example.com
```

Outcome: downloads the target's scripts, extracts the endpoint and subdomain references from their source, and prints the discovered directories in the terminal.

### Collect findings to a file silently

```
dirscraper -u https://example.com -o found.txt -s
```

Outcome: appends the discovered paths to found.txt (created if missing, appended if it exists) while suppressing terminal output — the batch-friendly form.

_No corpus evidence — derived from upstream documentation (project README)._
