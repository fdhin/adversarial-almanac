---
name: "dirhunt"
categories:
  - blackarch-webapp
version: "329.a5ea20d"
homepage: "https://github.com/Nekmo/dirhunt"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "Nekmo/dirhunt"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 2010
github-pushed: "2023-10-29"
github-archived: false
github-issues: 15
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dirhunt

> Web crawler that finds directories without brute force.

## What it's for

Dirhunt locates interesting directories on a site by crawling and analysis rather than wordlist brute force. It processes "Index Of" listing pages and reports interesting files, follows redirects, detects fake-404 error pages and blank index files placed to hide content, and pulls additional paths from robots.txt, Google, CommonCrawl, VirusTotal, crt.sh, SSL certificates and the Wayback Machine. Runs typically finish in seconds to tens of seconds because it minimizes requests. Proxies, inter-request delays and flags-based result filtering are supported.

## Typical use cases

### Crawl a site for directories

```
dirhunt https://example.com/
```

Outcome: crawls the site and prints discovered directories tagged with flags (index-of, fake 404, blank index, redirector) along with interesting files found in listings.

### Scan several sites and export a report

```
dirhunt https://example.com/ https://target.example.com/ --to-file report.json
```

Outcome: processes both sites in one run and writes a machine-readable JSON report of all findings.

### Control sources and pace

```
dirhunt https://example.com/ --exclude-sources google,virustotal -d 1 -p http://198.51.100.10:8080
```

Outcome: skips the named passive sources, waits one second between requests, and alternates requests through the given proxy.

_No corpus evidence — derived from upstream documentation (README and the project's usage reference)._
