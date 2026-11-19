---
name: "reverseip"
categories:
  - blackarch-recon
version: "13.42cc9c3"
homepage: "https://github.com/lolwaleet/ReverseIP"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "lolwaleet/ReverseIP"
github-stars: 55
github-pushed: "2020-09-30"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# reverseip

> Interactive Ruby script that lists every domain hosted on a given IP or domain, annotated with each site's HTTP status code and CMS.

## What it's for

ReverseIP (`revIP.rb`) performs reverse IP lookups: give it an IP address or domain and it queries a public reverse-IP web API (yougetsignal) for all domains sharing that host, then visits each result to fetch its HTTP status code and guess its CMS. It is fully interactive — no command-line arguments; it prompts for the target — and prints a colorized, numbered table of co-hosted domains. It depends on the Ruby `mechanize` and `colorize` gems. Its accuracy is bounded by the third-party API (rate limits and incomplete coverage), so treat the output as a lead list, not an exhaustive hosting inventory. For the same job from a shell pipeline, see `ripdc` (bash) — and note `revipd` (Python 2, different author) is an unrelated tool of the same name family whose upstream is gone.

## Typical use cases

### Enumerate domains sharing a host

```
ruby revIP.rb
```

Outcome: the script prompts `IP/Domain -->`; enter a domain or IP and it POSTs to the reverse-IP API, then prints the target, the domain count and a numbered list of every co-hosted domain, each annotated with its live HTTP status code and detected CMS — the classic pivot from one web host to its neighbors.

*No corpus evidence — derived from upstream documentation (README and revIP.rb source).*
