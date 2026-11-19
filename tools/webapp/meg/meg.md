---
name: "meg"
categories:
  - blackarch-webapp
version: "87.9daab00"
homepage: "https://github.com/tomnomnom/meg"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2021-04-27"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "tomnomnom/meg"
github-stars: 1716
github-pushed: "2024-02-03"
github-archived: false
github-issues: 40
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# meg

> Fetches many paths across many hosts without hammering any single host.

## What it's for

meg (by TomNomnom, Go) requests one path against *all* hosts before moving to the next path, and rate-limits per host by default (5 seconds between requests to the same host, 20 concurrent requests overall). That ordering — path-major instead of host-major — makes it the right tool for checking a shared set of paths (`/robots.txt`, `/.well-known/security.txt`, `/package.json`) against a large inventory of hosts: you get results quickly while no individual host is flooded. Every request/response pair is saved verbatim under an output directory with an index file, so the results are grep-able evidence rather than a summarized list.

## Typical use cases

### Run with the default file layout

Prepare `paths` (one path per line) and `hosts` (one `scheme://host` per line) in the working directory, then run with no arguments.

```
meg
```

Outcome: silently fetches every path on every host and writes raw request/response pairs to `./out/<host>/<hash>` plus an index at `./out/index` mapping each file to its URL and status.

### Watch progress with explicit files

```
meg -v paths.txt hosts.txt results
```

Outcome: prints a line per request (`results/example.com/<hash> http://example.com/robots.txt (404 Not Found)`) while saving to the `results` directory instead of `./out`.

### Check a single path everywhere

```
meg /.env hosts.txt
```

Outcome: requests just that one path against every host in the file — the shortest loop from "path worth checking" to a per-host answer set.

### Keep only the interesting statuses

```
meg -s 200 / hosts.txt
```

Outcome: saves full responses only for status-200 requests (combine with `-X HEAD` for a cheap existence sweep), leaving the output directory free of noise.

No corpus evidence — derived from upstream documentation.
