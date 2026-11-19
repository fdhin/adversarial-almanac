---
name: "dsstore-crawler"
categories:
  - blackarch-webapp
version: "7.efa51f5"
homepage: "https://github.com/anantshri/DS_Store_crawler_parser"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "anantshri/DS_Store_crawler_parser"
github-stars: 56
github-pushed: "2023-06-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dsstore-crawler

> A parser + crawler for .DS_Store files exposed publically.

## What it's for

dsstore-crawler exploits exposed `.DS_Store` files — the macOS Finder directory indexes that web servers so often leak. Given a starting URL it downloads the `.DS_Store` in that directory, parses every filename entry out of the binary format, probes each discovered path with a HEAD request, prints what exists with its status code, and recurses into every directory it finds, downloading and parsing that directory's `.DS_Store` in turn. Reach for it during web enumeration when a single leaked `.DS_Store` should be turned into a full hidden-path tree; it does nothing beyond `.DS_Store` files (no other backup/artifact formats) and sends randomized browser User-Agents while ignoring TLS certificate errors. The BlackArch package wraps the upstream `dsstore_crawler.py` as `/usr/bin/dsstore-crawler`.

## Typical use cases

### Crawl a leaked .DS_Store recursively

The single supported invocation: a starting URL.

```
dsstore-crawler -i http://target.example.com/
```

Outcome: fetches `/.DS_Store`, lists every entry it contains, prints each probed path as `http://target.example.com/<name> : <status>` and recurses into found directories (fetching their `.DS_Store` too) until the tree is exhausted — a hidden-path map of the host grown from one leaked index file.

### Start from a specific directory

Begin the crawl where an index was already spotted.

```
dsstore-crawler -i http://target.example.com/assets/
```

Outcome: same recursive parse-and-probe loop, seeded at `/assets/.DS_Store`, limiting the crawl to that subtree — useful when only one directory is known to leak the file.

No corpus evidence — derived from upstream source (argparse interface of `dsstore_crawler.py`; the README documents behavior only).
