---
name: "hexhttp"
categories:
  - blackarch-scanner
version: "v2.4.r1.g3d8b5c8"
homepage: "https://github.com/c0dejump/HExHTTP"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "c0dejump/HExHTTP"
github-stars: 765
github-pushed: "2026-09-06"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---

# hexhttp

> Web cache-poisoning and HTTP-header behavior scanner that replays crafted header requests at URLs and reports confirmed poisonings and odd server responses.

## What it's for

HExHTTP (c0dejump, Python 3.7+) runs a battery of HTTP header tests against a target: unkeyed-header cache poisoning and CPDoS payloads, debug and uncommon header detection, vhost and `Host: localhost` handling, HTTP method and version behavior, and server-error responses, with WAF detection along the way. Results appear on the console and as an HTML report, and confirmed findings can be pushed straight into a Burp proxy. It surfaces behaviors and candidate vulnerabilities for manual confirmation — it is not an exploit framework.

## Typical use cases

### Scan a single URL

```
hexhttp -u https://target.example.com/
```

Outcome: runs the full header-behavior battery, printing observed behaviors and confirmed cache-poisoning findings; add `-o` to also write an HTML report under `results/`.

### Scan a URL list at human pace

```
hexhttp -f urls.txt -t 20 -hu r
```

Outcome: scans every URL in the file with 20 threads and randomized human-like delays between requests, reducing the chance of tripping rate limits or a WAF.

### Restrict the run to cache-poisoning modules

```
hexhttp -u https://target.example.com/ --ocp
```

Outcome: only the cache-poisoning and CPDoS checks execute, skipping the other header probes — a fast pass when only poisoning matters.

### Forward findings to Burp Suite

```
hexhttp -u https://target.example.com/ --burp 127.0.0.1:8080
```

Outcome: confirmed vulnerabilities and interesting behaviors are replayed to the Burp proxy listener as Medium/High findings for manual analysis, while ordinary scan traffic stays direct.

*No corpus evidence — derived from upstream documentation.*
