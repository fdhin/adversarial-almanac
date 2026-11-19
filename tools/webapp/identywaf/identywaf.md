---
name: "identywaf"
categories:
  - blackarch-webapp
version: "208.3e535b7"
homepage: "https://github.com/stamparm/identYwaf"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "stamparm/identYwaf"
github-stars: 758
github-pushed: "2026-09-10"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# identywaf

> Blindly fingerprints the web application firewall in front of a site from how it reacts to non-destructive trigger payloads.

## What it's for

identYwaf (by stamparm; the packaged `identywaf` command wraps identYwaf.py) identifies which WAF product — from a catalog of ~95 (ModSecurity, Cloudflare, Imperva, Wallarm, Wordfence, Palo Alto and friends) — sits in front of a target, purely by blind inference: it sends a series of predefined, non-destructive payloads (e.g. `?aeD0oowi=1 AND 2>1`) and matches the provoked responses against characteristic per-product signatures. No banner, cookie or header name is required to be honest. TLS certificate validation is disabled by design since assessment targets commonly run expired or self-signed certs, and stuck HTTPS heuristics are retried over plain HTTP. It has no third-party Python dependencies.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Identify the WAF protecting a URL

The single-argument default run.

```
identywaf https://example.com/
```

Outcome: fires the payload battery at the target and prints the identified WAF product name (with its matching signature evidence); no output match means no known-WAF signal was provoked.

### Probe through a proxy, slowed and disguised

Observe each probe in Burp while keeping a low profile.

```
identywaf --proxy http://127.0.0.1:8080 --delay 1 --random-agent https://example.com/
```

Outcome: all payloads are sent through the intercepting proxy, one per second, each with a random User-Agent — the full identification run visible and replayable in the proxy's history.

### Teach it the target's block signature

Improve inference where rejection pages are custom.

```
identywaf --code 403 --string "blocked" https://example.com/
```

Outcome: heuristics additionally treat responses with status 403 containing `blocked` as the WAF's rejection behavior, tightening detection when a product's block page is customized.
