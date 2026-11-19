---
name: "xsstracer"
categories:
  - blackarch-scanner
version: "5.f2ed21a"
homepage: "https://github.com/1N3/XSSTracer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "1N3/XSSTracer"
github-stars: 132
github-pushed: "2016-01-23"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xsstracer

> One-shot checker for Cross-Site Tracing, clickjacking/X-Frame-Options, and Host Header Injection on a single host:port.

## What it's for

XSSTracer (1N3/CrowdShield, v1.3, 2016) is a small Python script that probes a single web server for four quick header/method weaknesses in one run: whether HTTP TRACE is enabled (Cross-Site Tracing, which can leak auth headers to script), whether the response lacks clickjacking/Cross-Frame-Scripting defenses (`X-Frame-Options` and friends), and whether the server mishandles a spoofed Host header (Host Header Injection, the basis of cache poisoning and password-reset poison). It takes a host and a port — nothing else — making it a fast first-pass check to fold into per-target triage. It does not hunt stored/reflected XSS payloads despite the name, and it tests exactly the endpoints you give it.

## Typical use cases

### Check a plain-HTTP server

```
./xsstracer target.example.com 80
```

Outcome: sends the TRACE probe and header checks against port 80 and prints a short per-check verdict — which of TRACE/clickjacking/host-header weaknesses are present on that listener.

### Check a TLS listener

```
./xsstracer target.example.com 443
```

Outcome: same four checks over the HTTPS port — run both ports when a site listens on 80 and 443, since hardening often differs between them.

*No corpus evidence — derived from upstream documentation.*
