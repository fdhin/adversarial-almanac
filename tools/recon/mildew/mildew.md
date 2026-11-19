---
name: "mildew"
categories:
  - blackarch-recon
version: "11.df49c23"
homepage: "https://github.com/daehee/mildew"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-27"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "daehee/mildew"
github-stars: 96
github-pushed: "2021-01-21"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mildew

> Builds a fresh list of official .mil domains from DoD directories and certificate logs.

## What it's for

Mildew seeds US Department of Defense vulnerability-disclosure recon with a domain list: it crawls the DoD-maintained website directories (defense.gov, af.mil, army.mil, navy.mil), scrapes out every unique `.mil` domain, then queries certificate transparency (crt.sh) per discovered root domain to pull additional subdomains into the list. It exists because no official public directory of the .mil zone exists — it is a scraper for the closest thing to one. It takes no arguments and does no live testing of the targets; the output is the starting corpus for your own resolution and scanning pipeline. Written in Go (2020-2021, unmaintained since). No corpus evidence — derived from upstream documentation.

## Typical use cases

### Generate the dot-mil domain list

Pull the current DoD domain corpus.

```
mildew
```

Outcome: crawls the four DoD directories and crt.sh, printing progress as it goes, and writes the deduplicated domain list to `mildew.out` in the working directory (one domain per line) — feed it to a resolver or subdomain tool to find live hosts.
