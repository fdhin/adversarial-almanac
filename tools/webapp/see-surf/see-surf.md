---
name: "see-surf"
categories:
  - blackarch-webapp
version: "v3.0.r27.g2d15238"
homepage: "https://github.com/In3tinct/See-SURF"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-06"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "In3tinct/See-SURF"
github-stars: 363
github-pushed: "2026-02-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 46
gh-activity-checked: "2026-11-09"
---

# see-surf

> Crawl a web application and flag URL parameters that are potential SSRF injection points.

## What it's for

See-SURF finds potential Server-Side Request Forgery parameters in a web application. A
multi-threaded crawler walks the target and applies regex heuristics (keywords like `url`,
`website`, `host`, IP-shaped values) to rank parameters worth attacking, then optionally
validates findings: reflected/non-blind SSRF is confirmed by watching for leaked internal data,
and blind SSRF by out-of-band callbacks to a webhook domain. It accepts Burp Suite sitemap XML
as crawl input and can optionally call an AI provider to analyze responses and generate payloads.
It does not exploit SSRF beyond validation — the output is a shortlist of parameters and evidence.

## Typical use cases

### Crawl a site for potential SSRF parameters

Quick unauthenticated pass over a target.

```
see-surf -H https://target.example.com
```

Outcome: the crawler walks the site and prints each potential SSRF parameter it encounters with
the endpoint it was found on; add `-v` to repeat the same parameter across every endpoint it
appears on (noisy but catches inconsistent sanitization).

### Scan behind a login

Reuse an authenticated session for the crawl.

```
see-surf -H https://target.example.com -c "session=Password1 other_cookie=value2"
```

Outcome: the supplied cookies ride along on every request, so parameters only reachable after
login are analyzed too.

### Seed the crawl from a Burp sitemap

Feed already-spidered requests instead of crawling from scratch.

```
see-surf -H https://target.example.com -c "session=Password1" -b burp_sitemap.xml
```

Outcome: every URL and parameter observed in the exported Burp sitemap is parsed for SSRF
candidates before any crawling happens, widening coverage to endpoints the built-in crawler
would not reach.

### Confirm blind SSRF with out-of-band callbacks

Route candidate parameters at a callback domain you watch.

```
see-surf -H https://target.example.com -e http://ATTACKER_IP
```

Outcome: payloads point at the given external domain instead of the default webhook service;
any HTTP callback observed there confirms the parameter issues server-side requests.

No corpus evidence — derived from upstream documentation (README).
