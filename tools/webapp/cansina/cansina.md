---
name: "cansina"
categories:
  - blackarch-webapp
version: "59.67c6301"
homepage: "https://github.com/deibit/cansina"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2023-05-04"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "deibit/cansina"
github-stars: 895
github-pushed: "2024-06-07"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cansina

> Python web content discovery tool that tells disguised 404s from real finds.

## What it's for

Cansina does forced browsing: web apps rarely publish every resource, so the only way to find them is to request candidates and judge the response. Cansina hammers a target with a wordlist ("payload file") and filters the responses — by status code, size and content — to separate existing resources from soft/404 pages that masquerade as hits. It supports multithreading, multiple extensions per word, custom headers, cookies, basic auth, proxies, path recursion, resuming, and keeps results in a SQLite database with CSV export. Not a vulnerability scanner: it finds what exists, you decide what to do with it.

## Typical use cases

### Discover hidden paths with a wordlist

Basic content discovery against one site.

```
cansina -u http://target.example.com -p wordlist.txt
```

Outcome: requests every wordlist entry against the target and prints the interesting ones (existing resources that are not disguised 404s), while storing all results in a SQLite database under `output/` for later review or CSV export.

### Speed up large runs

Cut bandwidth and interface overhead for big wordlists.

```
cansina -u http://target.example.com -p wordlist.txt -H -t 10 --no-progress
```

Outcome: `-H` switches requests to HEAD (no page body downloaded), `-t 10` raises the worker count, and `--no-progress` drops the terminal UI — the fastest combination for huge lists, at the cost of content-based filtering accuracy.

### Review stored results as a tree

Visualise what a previous scan found.

```
cansina -V output/http_target.example.com.sqlite
```

Outcome: reads the named scan database and renders its stored 200-status findings as an ASCII tree, giving a quick site-map overview of everything the scan uncovered.

No corpus evidence — derived from upstream documentation.
