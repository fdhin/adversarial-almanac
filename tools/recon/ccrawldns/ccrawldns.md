---
name: "ccrawldns"
categories:
  - blackarch-recon
version: "v1.0.r2.gdfd5062"
homepage: "https://github.com/lgandx/CCrawlDNS"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-1.0-or-later
meta-checked: "2026-11-09"
github-repo: "lgandx/CCrawlDNS"
github-stars: 181
github-pushed: "2025-12-25"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# ccrawldns

> Mine the Common Crawl archives for every subdomain ever recorded under a domain.

## What it's for

CCrawlDNS (by Laurent Gaffié, author of Responder) queries the Common Crawl index API — petabytes of crawled web data going back to 2008 — and extracts all subdomains collected for a target domain, storing results in a local database. Scans are tunable by year and by the number of crawl indices per year, with automatic request throttling, automatic path fingerprinting and web-language fingerprinting. It is purely passive: no packet is sent to the target, and hosts that only ever existed in old crawls still turn up — a useful complement to live DNS brute-forcing.

## Typical use cases

### Fast sweep of recent crawls

Get today's subdomain set in the fewest queries.

```
ccrawldns -d example.com --years last2 --max-per-year 1
```

Outcome: harvests subdomains recorded in the most recent two crawl years using one index per year (the most efficient mode) and stores the unique names in the local database for review.

### Target specific years

Restrict the search to crawl indices you care about.

```
ccrawldns -d example.com --years 2025,2021 --max-per-year 1
```

Outcome: only the 2025 and 2021 indices are queried — useful to compare the domain's footprint between two points in time.

### Full historical sweep

Pull everything the archive has ever seen.

```
ccrawldns -d example.com --years all --max-per-year 1
```

Outcome: walks every available index from 2008 onward — much slower, but yields the most complete historical subdomain set, including long-decommissioned hosts.

*No corpus evidence — derived from upstream documentation.*
