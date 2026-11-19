---
name: "hsecscan"
categories:
  - blackarch-scanner
version: "67.aa5845b"
homepage: "https://github.com/riramar/hsecscan"
packaged-by: [blackarch]
blackarch-builddate: "2025-02-01"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "riramar/hsecscan"
github-stars: 298
github-pushed: "2025-01-19"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hsecscan

> Python 2 scanner that audits HTTP response headers against a local SQLite knowledge base of security guidance, recommendations and CWE mappings.

## What it's for

hsecscan (riramar) fetches a URL, prints the response headers, and annotates each one from its `hsecscan.db` database: what the header does, its security implications, references (RFC/OWASP), recommendations, and the associated CWE entries. It supports POST bodies, proxies, custom User-Agents, redirect display and disabled certificate verification, and the header database can also be queried directly without scanning anything. Being Python 2 era, use `-i` on endpoints whose certificates modern clients reject.

## Typical use cases

### Audit a site's response headers

```
hsecscan -u https://example.com/
```

Outcome: prints the response headers followed by per-header security details — description, references, recommendations and CWE — flagging missing or misconfigured security headers.

### Scan through a proxy with a POST body

```
hsecscan -u https://target.example.com/ -d '{ "q": "test" }' -x 127.0.0.1:8080 -U "Mozilla/5.0"
```

Outcome: sends the JSON POST through the proxy with the chosen User-Agent and applies the same header analysis to that response — for example to audit an authenticated endpoint reached via an intercepting proxy.

### Query header knowledge without scanning

```
hsecscan -H Strict-Transport-Security
```

Outcome: prints the database entry for that header (security description, references, CWE); `-p` and `-P` list the enabled headers or the entire database.

*No corpus evidence — derived from upstream documentation.*
