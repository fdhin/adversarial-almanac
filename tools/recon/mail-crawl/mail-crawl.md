---
name: "mail-crawl"
categories:
  - blackarch-recon
version: "0.1"
homepage: "https://raw.githubusercontent.com/galkan/tools/master/mail-crawl/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# mail-crawl

> Minimal Python 2 crawler that fetches a page (or follows every link it finds) and prints every email address matching your chosen domain.

## What it's for

mail-crawl (by Galkan, 2013) harvests email addresses from websites. It fetches pages with a randomly rotated browser User-Agent, extracts `href` links and email addresses by regular expression, and prints each address it finds. `--mail` restricts the harvest to one domain (`@example.com`) or accepts `all` for any domain; `--time` inserts a random sleep between requests to look less like a crawler. It is a single-purpose Python 2 script — no recursion limits, no JS rendering — so for anything beyond a simple static site, a maintained harvester (theHarvester and similar) is the better tool.

## Typical use cases

### Harvest addresses from a single page

```
mail-crawl --url https://www.example.com --mail example.com
```

Outcome: fetches only the given URL and prints every `@example.com` address found in the HTML, one per line; `--mail all` prints addresses at any domain instead.

### Crawl the whole site slowly

```
mail-crawl --url-all https://www.example.com --mail example.com --time 3
```

Outcome: follows the links discovered from the start URL (not just the first page), sleeping a random interval scaled by `--time` between requests, printing each matching address as it is found.

*No corpus evidence — derived from the packaged source itself (galkan/tools mail-crawl.py, argparse verified).*
