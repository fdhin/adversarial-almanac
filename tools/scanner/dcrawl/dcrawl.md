---
name: "dcrawl"
categories:
  - blackarch-scanner
version: "7.3273c35"
homepage: "https://github.com/kgretzky/dcrawl"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-26"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "kgretzky/dcrawl"
github-stars: 548
github-pushed: "2019-07-14"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dcrawl

> Branch-limited threaded web crawler that fans out from one URL and collects unique hostnames rather than content.

*No corpus evidence — derived from upstream documentation (kgretzky/dcrawl README usage block).*

## What it's for

dcrawl (Kuba Gretzky) is a domain harvester built on a disciplined crawl: starting from one URL it follows `<a href>` links recursively, queueing and re-crawling every URL it finds, but with guards that keep a run from exploding — only a set number of links followed per hostname, a cap on distinct subdomains per domain (so one blogspot or CDN host does not swallow the crawl), text/html-only targets, a 1MB body limit, and inaccessible domains dropped. The output file is one unique hostname per line, and runs are resumable: feeding the previous output's domains back re-seeds the queue. It does no vulnerability checking and no content analysis — it is purely a tool for growing a target list from a seed page.

## Typical use cases

### Crawl a site into a hostname list

```
dcrawl -url http://target.example.com -out domains.txt -t 8
```

Outcome: crawls outward from the seed URL across 8 threads, writing every unique hostname encountered to `domains.txt`, one per line, while progress prints to stdout. Re-run with the same `-out` file (or seed from it) to resume and extend the list.

### Tune the branching guards for breadth

```
dcrawl -url http://target.example.com -out domains.txt -mu 10 -ms 25 -v
```

Outcome: `-mu` raises the links followed per hostname (default 5) and `-ms` the distinct subdomains allowed per domain (default 10), trading runtime for breadth on dense sites; `-v` logs each fetched URL. Lower both when a crawl stalls on shared-hosting or CDN domains.
