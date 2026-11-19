---
name: "shosubgo"
categories:
  - blackarch-recon
version: "V5.0.r0.gccb6dc7"
homepage: "https://github.com/incogbyte/shosubgo"
packaged-by: [blackarch]
blackarch-builddate: "2025-09-07"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "incogbyte/shosubgo"
github-stars: 584
github-pushed: "2025-09-01"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shosubgo

> Pull a domain's known subdomains straight out of the Shodan API.

## What it's for

Shosubgo is a minimal Go utility that asks Shodan for everything indexed under a domain and prints the distinct subdomains found. It is a useful complement to DNS brute-forcing in bug-bounty and external recon: Shodan's crawl history surfaces hosts that no longer resolve or that nameserver wordlists miss, with no packets sent to the target. A Shodan API key is required (the free tier works with limited quota). One domain or a file of domains per run.

## Typical use cases

### Enumerate one domain

```
shosubgo -d example.com -s SHODAN_API_KEY
```

Outcome: prints each subdomain of the domain that Shodan has indexed, one per line — pipe into sort -u for a clean list.

### Process a file of domains strictly

```
shosubgo -f domains.txt -s SHODAN_API_KEY -fail
```

Outcome: runs the same lookup for every domain in the file; -fail aborts on the first error (such as an invalid API key) instead of continuing with silent per-domain failures.

*No corpus evidence — derived from upstream documentation.*
