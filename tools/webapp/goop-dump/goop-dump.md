---
name: "goop-dump"
categories:
  - blackarch-webapp
version: "74.5865759"
homepage: "https://github.com/nyancrimew/goop"
packaged-by: [blackarch]
blackarch-builddate: "2025-05-11"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "nyancrimew/goop"
github-stars: 653
github-pushed: "2026-09-04"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# goop-dump

> Completeness-focused git repository dumper for websites, handling listing-less .git, pack files and other edge cases.

## What it's for

goop downloads a website's .git repository as completely as possible, compared to tools that stop at the bare minimum: if directory listing is available it mirrors .git recursively; otherwise it reconstructs refs and objects from HEAD/index/logs/packed-refs, walks each commit to its parents, runs `git checkout .`, then keeps going — fetching files listed in the git index and in .gitignore and creating objects for them. The upstream repo moved with its author (nyancrimew → deletescape; the Go module path is `github.com/deletescape/goop`) and the codebase is heavily inspired by arthaud/git-dumper. It dumps what is reachable; it is not a history/secrets miner.

## Typical use cases

### Dump one site's .git

```
goop example.com
```

Outcome: detects whether directory listing is available, mirrors or reconstructs the repository into `example.com/` (or a given DIR), checks out the working tree, and reports what was recovered.

### Resume a dump across server rate limits

```
goop --keep --force http://target.example.com/.git dumped
```

Outcome: re-runs into the existing directory, keeping already-downloaded files and fetching only the remainder — useful when repeated object requests trigger rate limiting.

### Dump many domains from a list

```
goop --list domains.txt
```

Outcome: reads domain names one per line and dumps each site's .git in turn, continuing past individual failures.

No corpus evidence — derived from upstream documentation.
