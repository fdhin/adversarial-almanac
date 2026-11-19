---
name: "log4j-scan"
categories:
  - blackarch-webapp
version: "88.07f7e32"
homepage: "https://github.com/fullhunt/log4j-scan"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "fullhunt/log4j-scan"
github-stars: 3423
github-pushed: "2022-11-23"
github-archived: false
github-issues: 34
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# log4j-scan

> Fully automated scanner for Log4j RCE (CVE-2021-44228) with DNS-callback validation.

## What it's for

FullHunt's scanner (by Mazin Ahmed) for the Log4Shell RCE. It fuzzes more than 60 HTTP request headers plus POST form and JSON body parameters with JNDI lookup payloads, and validates findings through out-of-band DNS callbacks — it integrates the interact.sh callback provider out of the box, so no separate callback server setup is needed. It also carries WAF-bypass payloads and dedicated detection modes for the CVE-2021-45046 patch bypass and the Apache Commons Text RCE (CVE-2022-42889). Intended for security teams scanning their own, authorized infrastructure; it confirms callback reachability, not full code execution.

## Typical use cases

### Scan one URL across all request styles

```
log4j-scan -u https://target.example.com --run-all-tests
```

Outcome: probes the target with GET, POST (url-encoded form), and JSON body payloads across all fuzzed headers; prints which requests triggered a DNS callback at the interact.sh-hosted probe domain.

### Hunt for WAF bypasses

```
log4j-scan -u https://target.example.com --waf-bypass
```

Outcome: extends the scan with obfuscated payload variants; a callback from one of these while the plain payload was blocked indicates the WAF in front of the app is bypassable.

### Sweep a URL list with a custom callback host

```
log4j-scan -l urls.txt --custom-dns-callback-host callback.example.com
```

Outcome: iterates every URL in the file, tagging payloads with your own callback domain so hits are attributable to specific targets; check the callback service's query log to identify which host resolved the lookup.

### Test specifically for the v2.15.0 patch bypass

```
log4j-scan -u https://target.example.com --test-CVE-2021-45046
```

Outcome: sends only the CVE-2021-45046 detection payloads, confirming whether a Log4j 2.15.0 instance remains exploitable despite the initial fix.

No corpus evidence — derived from upstream documentation.
