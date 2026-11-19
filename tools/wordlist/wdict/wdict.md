---
name: "wdict"
categories:
  - blackarch-wordlist
version: "v0.1.20.r0.g05d896c"
homepage: "https://github.com/pyqlsa/wdict"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "pyqlsa/wdict"
github-stars: 6
github-pushed: "2026-08-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# wdict

> Build custom wordlists by scraping webpages or crawling local files.

## What it's for

A Rust CLI that crawls a website (or reads local files), extracts words from the content, filters them, and writes a deduplicated dictionary — in the same niche as CeWL-style scrapers but with unusual control over the output: minimum/maximum word length, unicode normalization filters, per-site crawl policy (same host / subdomains / siblings / all), request rate limiting, and resumable crawls. Typical purpose: generating a targeted password-cracking wordlist from a target's own site content, or a corpus from local documents. It only produces lists — cracking and fuzzing are left to other tools.

## Typical use cases

### Build a password wordlist from a target site

```
wdict -u https://target.example.com -d 2 -m 6 -o target-words.txt --filters to-lower,any-numbers
```

Outcome: crawls the site to depth 2 under the default same-host policy at 10 requests per second, extracts words of at least six characters, lowercases them, drops all-number tokens, and writes the dictionary to `target-words.txt` (the default output name is `wdict.txt` when `-o` is omitted).

### Build a dictionary from local files

```
wdict -p ./docs -o docs-words.txt --append
```

Outcome: crawls local files under `./docs` for words and appends them to `docs-words.txt`, merging site-scraped and local corpora into a single list (`--append` grows an existing dictionary instead of overwriting it).

### Resume an interrupted crawl

```
wdict -u https://target.example.com -d 3 --resume
```

Outcome: continues a previous crawl from its saved state file (`state-wdict.json`, written automatically), skipping already-crawled pages so long crawls survive interruptions without restarting — `--resume-strict` instead enforces every argument recorded in the state file.

*No corpus evidence — derived from upstream documentation.*
