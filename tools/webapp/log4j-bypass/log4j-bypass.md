---
name: "log4j-bypass"
categories:
  - blackarch-webapp
version: "33.f5c92f9"
homepage: "https://github.com/cyberqueen-meg/log4j-bypass"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "cyberqueen-meg/log4j-bypass"
github-stars: 24
github-pushed: "2022-07-31"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# log4j-bypass

> Log4Shell tester that fires every known WAF-bypass payload variant.

## What it's for

A fork of fullhunt/log4j-scan modified to test all published Log4j (CVE-2021-44228) bypass encodings at once — the nested-interpolation, unicode-escape, and casing tricks that slip JNDI lookups past filtering WAFs — instead of a single canonical payload. Give it a URL or a list plus a DNS callback host (a CanaryToken, dnslog.cn, or your own interactsh/Burp Collaborator domain); any backend that resolves the payload produces a DNS hit on your callback. Detection is out-of-band only, so you must have a callback service running to see results. Use only against systems you are authorized to test.

## Typical use cases

### Test one URL with a callback host

```
log4j-bypass -u https://target.example.com -c callback.example.com
```

Outcome: sends requests carrying each bypass payload variant in HTTP headers and prints which payload was dispatched; a DNS lookup arriving at `callback.example.com` confirms the target is vulnerable.

### Scan a list of URLs with more threads

```
log4j-bypass -l urls.txt -c callback.example.com -t 10 -w 120
```

Outcome: iterates the URL file with 10 concurrent workers, then waits 120 seconds (`-w`) before exiting so late DNS callbacks still land while you watch the callback service.

### Route the test through an intercepting proxy

```
log4j-bypass -u https://target.example.com -c callback.example.com -p http://127.0.0.1:8080
```

Outcome: all probe requests pass through the proxy, letting you inspect exactly which payloads and headers were sent against each target.

No corpus evidence — derived from upstream documentation.
