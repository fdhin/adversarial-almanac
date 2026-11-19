---
name: "github-subdomains"
categories:
  - blackarch-recon
version: "v1.2.2.r1.g2bfdf12"
homepage: "https://github.com/gwen001/github-subdomains"
packaged-by: [blackarch]
blackarch-builddate: "2025-09-21"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "gwen001/github-subdomains"
github-stars: 878
github-pushed: "2023-03-28"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# github-subdomains

> Go tool that mines GitHub code search results for subdomains of a given domain.

## What it's for

This tool performs searches on GitHub and parses the results to find subdomains of a given domain — hostnames that appear in code, configuration files, and documentation committed to public repositories but often absent from DNS wordlists and certificate transparency feeds. It complements active subdomain enumeration with a purely passive, API-based source. It requires a GitHub token (single, comma-separated list, or a `.tokens` file with one per line); tokens that hit rate limits are automatically disabled for a minute and re-enabled. It does not touch the target's infrastructure at all.

## Typical use cases

### Find subdomains for a domain

```
github-subdomains -d example.com -t <github_token>
```

Outcome: runs its search signature set against the GitHub API, extracts and cleans subdomain matches, and prints unique `sub.example.com` names; the token can also be supplied as a comma-separated list or via the `GITHUB_TOKEN` environment variable.

### Save results to a file in raw form

```
github-subdomains -d example.com -t <github_token> -o example.txt -raw
```

Outcome: writes discovered subdomains to `example.txt` (default filename is `<domain>.txt`); `-raw` outputs the matches without deduplication/cleaning for manual review.

### Catch sibling domains in extended mode

```
github-subdomains -d example.com -t <github_token> -e
```

Outcome: extended mode also looks for `<dummy>example.<tld>` patterns — domains sharing the same SLD but living under other TLDs — widening the passive footprint beyond the exact zone.

*No corpus evidence — derived from upstream documentation.*
