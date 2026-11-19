---
name: "hookshot"
categories:
  - blackarch-webapp
version: "205.df30b85"
homepage: "https://github.com/andrew-vii/hookshot/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "andrew-vii/hookshot"
github-stars: 76
github-pushed: "2024-07-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hookshot

> Automates email scraping from a website with CeWL and checks every address against the HaveIBeenPwned breach API.

## What it's for

Hookshot chains two OSINT steps into one run: it spiders a target website to a given depth collecting email addresses (using CeWL as the harvester), then queries the HaveIBeenPwned API for each collected address and writes the breach comparison to a file. Use it to assess credential/breach exposure of an organisation's public web presence ahead of a credential-stuffing assessment or a report recommendation. It needs a valid HIBP API key file of your own and performs no exploitation — it only correlates public data. The packaged `hookshot` command wraps the repository's `main.py`.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Assess breach exposure of emails found on one site

Scrape, then check, in a single invocation.

```
hookshot hibp.key https://example.com 2 results.csv 30
```

Outcome: spiders example.com to depth 2 harvesting emails via CeWL, queries HIBP for each address (30-second timeout per request) using the API key in `hibp.key`, and writes the per-address breach results to `results.csv`.

### Batch multiple seed URLs

Assess several properties in one pass.

```
hookshot hibp.key urls.txt 3 out.csv 30
```

Outcome: reads one URL per line from `urls.txt`, crawls each to depth 3, and appends all harvested-and-checked addresses to the single output file.
