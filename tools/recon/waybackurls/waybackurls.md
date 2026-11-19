---
name: "waybackurls"
categories:
  - blackarch-recon
version: "11.89da10c"
homepage: "https://github.com/tomnomnom/waybackurls"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2021-04-27"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "tomnomnom/waybackurls"
github-stars: 4601
github-pushed: "2024-05-01"
github-archived: false
github-issues: 49
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# waybackurls

> Streams every URL the Wayback Machine has ever archived for a domain and its subdomains — one per line, built for pipelines.

## What it's for

waybackurls (tomnomnom) fetches archived URL lists for `*.domain` from the Wayback Machine's CDX API and prints them to stdout. It is the canonical "what did this site used to expose?" step of content discovery: old endpoints, forgotten parameters, deleted JS files and admin paths survive in archives long after they leave the live site — and the archived copies of JS/JSON responses are themselves retrievable from the Wayback Machine. Input is line-delimited domains on stdin (or a single domain as an argument); output is plain URLs, deduplicated in order of discovery, which is exactly what downstream tools (`gf`, `unfurl`, `qsreplace`, `httpx`) expect. It queries only the Wayback Machine — no Common Crawl or other archives (see waymore for multi-source) — and does no fetching or filtering itself.

## Typical use cases

### Pull every archived URL for a list of domains

Harvest historical endpoints for content discovery.

```
cat domains.txt | waybackurls > urls
```

Outcome: queries the Wayback CDX API for every subdomain of each line-delimited domain in `domains.txt` and writes all archived URLs to `urls`, one per line; run against a single domain with `waybackurls example.com`.

### Extract archived parameterized endpoints

Find old parameters worth retesting on the live application.

```
echo example.com | waybackurls | grep '=' | sort -u > params.txt
```

Outcome: filters the archived URL stream down to URLs carrying query strings — a quick inventory of historical parameter names to replay with modern tooling (`qsreplace` to swap in your own values). Add `--no-subs` to restrict the search to the exact domain, or `--dates` to prefix each URL with its capture date.

*No corpus evidence — derived from upstream documentation.*
