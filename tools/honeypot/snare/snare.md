---
name: "snare"
categories:
  - blackarch-honeypot
version: "188.b17fdfe"
homepage: "https://github.com/mushorg/snare"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "mushorg/snare"
github-stars: 487
github-pushed: "2024-06-10"
github-archived: false
github-issues: 33
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snare

> Web-application honeypot sensor that clones a real site and serves the copy to attackers, pairing with the tanner backend that makes the decoy react believably.

## What it's for

SNARE ("Super Next generation Advanced Reactive honEypot", from mushorg, the organization behind conpot) is a web honeypot built attack-surface-first: it clones an existing web page and serves that snapshot, so probes for plugin vulnerabilities, scanner enumeration and other malicious traffic hit a disposable copy instead of a real application. It is designed as a pair — SNARE is the lightweight sensor at the edge, while the separate tanner service is the central decision maker that evaluates each request (for example, deciding how a fake SQL injection should be answered), letting many distributed SNARE sensors share one brain. Reach for it when you want to measure how your web attack surface gets probed without exposing a real app. It requires Python 3.6+ and is run through two entry points: `clone` (snapshot a page) and `snare` (serve it).

## Typical use cases

### Clone a page to use as bait

Snapshot the site SNARE will impersonate.

```
sudo clone --target http://example.com --path /opt/snare-pages
```

Outcome: the cloner walks http://example.com and writes a local copy (bounded by `--max-depth` if given) under /opt/snare-pages/example.com, producing the page directory SNARE will serve.

### Serve the cloned surface

Run the sensor against the snapshot.

```
sudo snare --port 8080 --page-dir example.com --path /opt/snare-pages
```

Outcome: SNARE serves the cloned site on port 8080 — http://localhost:8080/index.html shows the copy — while every request, from casual browsing to scanner probes, is logged and (with a tanner service configured) forwarded for analysis.

*No corpus evidence — derived from upstream documentation.*
