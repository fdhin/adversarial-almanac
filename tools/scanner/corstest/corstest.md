---
name: "corstest"
categories:
  - blackarch-scanner
version: "10.beffd0b"
homepage: "https://github.com/RUB-NDS/CORStest"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "RUB-NDS/CORStest"
github-stars: 423
github-pushed: "2020-08-14"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# corstest

> Batch-checks a list of domains or URLs for CORS misconfigurations by sending probe Origin headers and inspecting the Access-Control-Allow-* responses.

*No corpus evidence — derived from upstream documentation (RUB-NDS/CORStest README; usage verified against corstest.py in the same repo).*

## What it's for

CORStest (Ruhr University Bochum, Network & Data Security group; built on James Kettle's CORS research) reads a file of domains or URLs and probes each with crafted `Origin` headers, classifying what comes back against the eight misconfiguration types it knows: origin reflection, insecure developer backdoors (JSFiddle/CodePen origins allowed), `null`-origin handling, pre-domain wildcards (`notdomain.com`), post-domain wildcards (`domain.com.evil.com`), allowed subdomains, non-SSL origins allowed on SSL resources, and invalid CORS header use. The authors call it quick & dirty for a reason: it is a triage scanner, not an exploiter — findings print to stdout per URL, and a misconfiguration usually only matters for data theft when `Access-Control-Allow-Credentials` is also present (that filter is the `-q` flag). Python 3, heavily parallel (default 32 processes), input is always a file.

## Typical use cases

### Scan a URL list for CORS misconfigurations

```
corstest -v urls.txt
```

Outcome: every entry is probed with multiple crafted origins; results print per URL as `<url> - Alert: <misconfiguration>` for findings, plus `Invalid:`/`Warning:` lines for weaker classes and `Not vulnerable` for clean entries. `-v` adds the detail block (Resource, Origin sent, ACAO, ACAC) under each finding so the exact misconfiguration can be read off the output.

### Keep only exploitable, credentialed-CORS findings

```
corstest -q -p 64 urls.txt
```

Outcome: `-q` restricts output to endpoints that also return `Access-Control-Allow-Credentials: true` — the subset actually exploitable for authenticated cross-origin reads; `-p 64` raises the process count (default 32) so large lists finish sooner.

### Probe authenticated endpoints with a cookie

```
corstest -c 'session=abc123' -s urls.txt
```

Outcome: the cookie is attached to every request, so the probe observes CORS behavior as an authenticated user rather than anonymously; `-s` forces HTTPS on bare-domain entries so the cookie is not sent in cleartext.
