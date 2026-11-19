---
name: "elite-proxy-finder"
categories:
  - blackarch-proxy
version: "51.1ced3be"
homepage: "https://github.com/DanMcInerney/elite-proxy-finder"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "DanMcInerney/elite-proxy-finder"
github-stars: 252
github-pushed: "2016-11-23"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# elite-proxy-finder

> Harvest public HTTP proxies and keep only the ones that are genuinely elite-anonymous, TLS-capable and fast.

## What it's for

elite-proxy-finder (Dan McInerney) automates sourcing working anonymous proxies: it scrapes proxy listings (gatherproxy.com), then tests every candidate concurrently through four checks — three IP-echo services, one of them over HTTPS to prove the proxy can carry TLS traffic, plus a response-header inspection confirming it is a true elite (L1) proxy that neither leaks the client IP nor advertises itself via forwarded headers. Results print with request time and country code, fastest first, so the head of the list is directly usable as egress. It finds HTTP proxies only (no SOCKS), is Python 2/gevent-era (2014–2015), and depends on the third-party listing site still being alive.

## Typical use cases

### Find and verify elite proxies

```
python elite-proxy-finder.py
```

Outcome: prints every proxy that passes all four tests with its country code and per-request timing, sorted fastest-first.

### Keep only the fastest few

```
python elite-proxy-finder.py -s 10
```

Outcome: limits output to the 10 quickest verified proxies.

### Get a clean list for scripting

```
python elite-proxy-finder.py -q
```

Outcome: prints only the IP and port of passing proxies — pipe the output straight into another tool.

### See failures too

```
python elite-proxy-finder.py -a
```

Outcome: prints all candidates including errors from each of the four tests — the debugging view when nothing passes (e.g. gevent too old, listing site unreachable).

*No corpus evidence — derived from upstream documentation.*
