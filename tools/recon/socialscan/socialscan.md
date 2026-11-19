---
name: "socialscan"
categories:
  - blackarch-recon
version: "128.5ae42d0"
homepage: "https://github.com/iojw/socialscan"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "iojw/socialscan"
github-stars: 1873
github-pushed: "2026-10-28"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# socialscan

> Checks usernames and email addresses for availability across social platforms by querying their registration servers directly, avoiding the false positives of page-scraping checkers.

## What it's for

Finding which handles or addresses are taken, free or invalid — accurately. Instead of fetching profile pages and reading status codes (which misclassifies reserved words like `admin` and deleted or banned accounts), socialscan replicates the platforms' own registration checks with the right CSRF tokens, headers and cookies, and runs every query concurrently (asyncio/aiohttp), so hundreds of checks finish in seconds. Username checks: Instagram, Twitter, GitHub, Tumblr, Lastfm, Snapchat, GitLab, Reddit, Yahoo. Email checks: Instagram, Twitter, GitHub, Tumblr, Lastfm, Pinterest, Firefox. It reports availability only — it does not collect profile contents — and is equally importable as a Python library (`socialscan.util.sync_execute_queries`).

## Typical use cases

### Check a handful of identifiers

```
socialscan user mail@example.com
```

Outcome: after all concurrent checks complete, a per-platform table shows each query as available, taken or invalid.

### Bulk-check from a file

```
socialscan --input queries.txt
```

Outcome: reads one username or email per line and checks them all concurrently — the practical form for triaging candidate names found during username enumeration.

### Report only what is free, as JSON

```
socialscan --available-only --json free.json --input queries.txt
```

Outcome: prints only unclaimed identifiers to the terminal and writes the full result set (query, platform, message, availability, validity) to `free.json`. Further useful flags: `-p/--platforms` to restrict the platform set (accepted names via `--help`), `-c/--cache-tokens` to cut request volume, and `--proxy-list` to spread queries over HTTP proxies.

*No corpus evidence — derived from upstream documentation (iojw/socialscan README).*
