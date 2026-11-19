---
name: "swamp"
categories:
  - blackarch-recon
version: "59.3c8be65"
homepage: "https://github.com/jakecreps/swamp"
packaged-by: [blackarch, aur]
blackarch-builddate: "2020-01-08"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# swamp

> OSINT pivot that turns a Google Analytics tracking ID (or a URL) into the list of sites sharing that tracker.

## What it's for

Many unrelated-looking sites are operationally linked because they embed the same Google Analytics tracking ID (UA-XXXXXXX-Y). Swamp, by Jake Creps, exploits that: give it a tracking ID or a seed URL and it looks up which other domains are associated with the same ID — a classic OSINT pivoting technique for mapping an organization's web footprint beyond the domains it publicly claims, de-anonymizing owners, or clustering phishing kits that reused a victim's tracker. It queries urlscan.io by default (free, no key) and can additionally use SpyOnWeb with an account token. The original GitHub repository is gone; the tool survives in distro packages, and the usage below is from the archived upstream README. One ID in, a list of associated URLs out — enrichment and attribution, not scanning.

## Typical use cases

### Pivot on a tracking ID

Enumerate sites sharing a Google Analytics ID.

```
swamp -id UA-1234567-89
```

Outcome: queries the configured APIs for the tracking ID and prints the unique URLs of sites associated with it — every domain sharing that tracker, likely the same operator or campaign.

### Pivot on a URL instead of an ID

Start from a known site when you do not have its tracker.

```
swamp -url https://www.example.com
```

Outcome: fetches the page, extracts its Google Analytics tracking ID (trying HTTPS first, then HTTP), and pivots to list all other sites carrying the same ID.

### Save results to a file

Keep the pivot output for the report.

```
swamp -id UA-1234567-89 -o results.txt
```

Outcome: full results are printed to screen and additionally written to results.txt.

### Use the SpyOnWeb source

Add a second data source for wider coverage.

```
swamp -id UA-1234567-89 -spyonweb -token YOUR_API_TOKEN
```

Outcome: also queries SpyOnWeb with the supplied account token, merging its associated-site findings into the results.

*No corpus evidence — derived from upstream documentation (archived README; the original repository is no longer available).*
