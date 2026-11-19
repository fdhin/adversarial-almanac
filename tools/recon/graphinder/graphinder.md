---
name: "graphinder"
categories:
  - blackarch-recon
version: "1.11.6"
homepage: "https://github.com/Escape-Technologies/graphinder"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "Escape-Technologies/graphinder"
github-stars: 228
github-pushed: "2026-11-05"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# graphinder

> Find GraphQL endpoints on a domain by combining subdomain enumeration, script analysis and bruteforce.

## What it's for

graphinder takes a root domain and hunts down its GraphQL endpoints three
ways: it enumerates subdomains, parses every script the discovered pages load
for endpoint references, and bruteforces well-known GraphQL paths, then
validates candidates with a precision mode that probes whether the endpoint
really speaks GraphQL — cutting false positives like a 404 JSON route. Reach
for it when mapping a bug-bounty wildcard or a large web estate before doing
introspection work on the found endpoints. Confirmed endpoints are written to
`results.json` (or `-o FILE`); script analysis and bruteforce can each be
disabled with `--no-script` / `--no-bruteforce` when speed matters or the
target is fragile, and `-w` tunes worker concurrency.

## Typical use cases

### Scan one domain with the default pipeline

```
graphinder -d example.com
```

Outcome: enumerates subdomains, crawls and analyzes scripts, bruteforces
candidate paths and writes every confirmed GraphQL endpoint to
`results.json` in the working directory.

### Sweep a domain list fast

```
graphinder -f domains.txt --no-script -w 20 -o results.json
```

Outcome: reads the domain list, skips script crawling for speed, runs up to
20 concurrent workers across domains and writes the confirmed endpoints to
results.json.

*No corpus evidence — derived from upstream documentation.*
