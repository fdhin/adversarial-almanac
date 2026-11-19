---
name: "corscanner"
categories:
  - blackarch-webapp
version: "1.0.1.r2.g593043f"
homepage: "https://github.com/chenjj/CORScanner"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "chenjj/CORScanner"
github-stars: 1164
github-pushed: "2021-11-25"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# corscanner

> CORS misconfiguration scanner covering the common bypass classes from the USENIX '18 CORS research.

## What it's for

CORScanner (from the authors of the USENIX Security 2018 CORS study) probes a site's cross-origin policy with crafted `Origin` headers — reflecting origins, wildcard values, unescaped-dot/underscore tricks, pre/post-domain and null-origin variants among others — and reports which misconfiguration classes the endpoint exhibits, i.e. whether a hostile origin can read credentialed responses. Concurrency comes from gevent, so it scales to domain lists. It can also be imported as a Python library (`from CORScanner.cors_scan import cors_check`). It tests policy responses only; turning a finding into actual data theft is a separate step for your browser/PoC.

## Typical use cases

### Check one domain verbosely

Single-target scan with live result display.

```
corscanner -u https://target.example.com -v
```

Outcome: sends the crafted Origin probes and prints, in real time, each misconfiguration type detected (e.g. `reflect_origin`) with the offending header values.

### Sweep a list of domains with concurrency

Large-scale scan from a URL file.

```
corscanner -i domains.txt -t 20 -o results.json
```

Outcome: checks every domain in `domains.txt` using 20 concurrent workers and writes the findings for all misconfigured hosts to `results.json`.

### Check through a proxy for verification

Replay the probes through an intercepting proxy.

```
corscanner -u https://target.example.com -p http://127.0.0.1:8080
```

Outcome: routes every probe request through the proxy so you can inspect the exact `Origin`/`Access-Control-Allow-*` exchange behind each finding.

No corpus evidence — derived from upstream documentation.
