---
name: "gospider"
categories:
  - blackarch-webapp
version: "108.f6cc9a7"
homepage: "https://github.com/jaeles-project/gospider"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-03-30"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "jaeles-project/gospider"
github-stars: 3018
github-pushed: "2024-04-21"
github-archived: false
github-issues: 57
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gospider

> Fast concurrent web spider in Go that also pulls endpoints from JS, robots, sitemaps and archival sources.

## What it's for

GoSpider crawls one or many sites in parallel and collects endpoints: it parses robots.txt and sitemap.xml, extracts and verifies links found inside JavaScript files (linkfinder-style), spots subdomains and AWS S3 buckets in response bodies, and can additionally pull known URLs from Archive.org, Common Crawl, VirusTotal and AlienVault. Output is grep-friendly (or JSON with `--json`), headers and cookies can be loaded from a Burp raw request, and URL filtering works via blacklist/whitelist regex. It only collects URLs — it does not test or attack them.

## Typical use cases

### Crawl a single site

```
gospider -s "https://target.example.com" -o output -c 10 -d 1
```

Outcome: crawls the site with 10 concurrent requests to depth 1, parsing robots/sitemap/JS along the way, printing discovered URLs to the console and writing per-source results under `output/`.

### Crawl a scope list in parallel

```
gospider -S sites.txt -o output -c 10 -d 1 -t 20
```

Outcome: runs 20 sites at once (each with 10 concurrent requests), one output subfolder per site — the standard batch pass over a recon target list.

### Pull historical URLs from third-party archives

```
gospider -s "https://target.example.com" --other-source --include-subs -o output
```

Outcome: additionally queries Archive.org, Common Crawl, VirusTotal and AlienVault for known URLs of the domain, subdomains included, surfacing endpoints that are no longer linked on the live site.

### Replay authenticated headers from a Burp request

```
gospider -s "https://target.example.com" --burp request.txt -o output
```

Outcome: loads headers and cookies from the captured Burp raw request and sends them with every crawl request, so the spider sees the authenticated view of the application.

No corpus evidence — derived from upstream documentation.
