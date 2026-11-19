---
name: "wafpass"
categories:
  - blackarch-webapp
version: "50.4211785"
homepage: "https://github.com/wafpassproject/wafpass"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "wafpassproject/wafpass"
github-stars: 222
github-pushed: "2023-10-16"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wafpass

> Replays a target parameter against every payload in its CSV sets to benchmark WAF and input-validation behavior.

## What it's for

WAFPASS (Hamed Izadi, wafpassproject) analyses a parameter with all payloads' bypass methods, aiming at benchmarking security solutions like WAFs: point it at a URL and it fires each bypass payload from its bundled CSV collections at the parameter, letting you compare which techniques the target blocks and which slip through. It supports HTTP and HTTPS, GET and POST requests, cookies for authenticated pages, custom user agents, request delays, intercepting proxies, and per-request random X-Forwarded-For spoofing. Payload groups are selectable (sql, xss, others, all) and the CSV files in the payloads/ directory are plain `payload@description` lines, so the corpus is user-extensible.

## Typical use cases

### Benchmark a parameter with the full payload set

The default run.

```
wafpass -u "http://target.example.com/page.php?parameter=value"
```

Outcome: sends every payload (all groups) at the target URL's parameter and prints the outcome of each request so you can see which bypass methods the protection lets through.

### Restrict testing to SQL-injection payloads

Cut run time and noise.

```
wafpass -u "http://target.example.com/page.php?parameter=value" -t sql
```

Outcome: replays only the sqli payload CSV group against the parameter (xss, others and all are the other selections).

### Watch through a proxy with randomized source IPs

Observe and evade rate rules at once.

```
wafpass -u "http://target.example.com/page.php?parameter=value" -x https://198.51.100.10:8080 -r
```

Outcome: routes every payload request through the proxy at 198.51.100.10:8080 (so each one is visible in your intercepting tool) while -r randomizes the X-Forwarded-For header per request.

### Include a session cookie

Test authenticated pages.

```
wafpass -u "http://target.example.com/page.php?parameter=value" -c "phpsessid=value"
```

Outcome: sends the cookie header on every payload request, letting you benchmark protection on pages restricted to authenticated users.

No corpus evidence — derived from the packaged upstream source (wafpass.py argparse and README of wafpassproject/wafpass).
