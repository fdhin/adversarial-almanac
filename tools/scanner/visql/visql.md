---
name: "visql"
categories:
  - blackarch-scanner
version: "49.3082e30"
homepage: "https://github.com/blackvkng/viSQL"
packaged-by: [blackarch]
blackarch-builddate: "2017-10-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "blackvkng/viSQL"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# visql

> SQL-injection sweep across every site on a server — reverse-IP lookup, crawl each domain, error-test every URL found.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Finding injectable pages at server scope rather than site scope. Give viSQL one site (or a bare IP); it runs a reverse-IP lookup to enumerate the other domains hosted there, crawls each site, and then tests every harvested URL for SQL injection, printing each hit grouped per site. It is a 2017 Python 2 tool by Black Viking (blackvkng) whose upstream repository has since disappeared — public forks carry the source, and BlackArch still ships a `visql` wrapper that runs viSQL.py under python2. It only detects; it does not exploit or dump, and its entire option surface is `-t`/`--target`.

## Typical use cases

### Sweep a server starting from one site

```
visql -t http://target.example.com
```

Outcome: reverse-IP lookup lists the co-hosted domains, each is crawled for URLs, and every URL is injection-tested; findings print as "SQLi vuln! --> <url>" grouped under each site, with timestamped progress lines per phase.

### Start from a bare IP

```
visql -t 198.51.100.10
```

Outcome: the same pipeline keyed off the IP address — every site resolving to it is enumerated, crawled and SQLi-scanned in one run.
