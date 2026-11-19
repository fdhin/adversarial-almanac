---
name: "rapidscan"
categories:
  - blackarch-webapp
version: "221.296a20b"
homepage: "https://github.com/skavngr/rapidscan"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "skavngr/rapidscan"
github-stars: 2139
github-pushed: "2023-08-22"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rapidscan

> Multi-tool web vulnerability scanner that orchestrates dozens of established scanners against one target.

## What it's for

rapidscan is a Python 3 wrapper, not a scanner itself: it runs a chain of
well-known tools (nmap, dnsrecon, wafw00f, uniscan, sslyze, fierce, lbd,
theharvester, amass, nikto, and more) plus custom checks against a single
host, then correlates the combined output. Its selling points are checking
the same vulnerability class with multiple tools to filter false positives,
and tagging findings with severity, OWASP Top 10 / CWE-25 mapping,
vulnerability definitions and remediation hints. It performs no exploitation.
Because it delegates everything, its results are only as good as the member
tools installed — the packaged dependencies cover the required set. Reach for
it when you want one broad automated sweep of a web target; run the
individual tools when you need fine-grained control.

## Typical use cases

### Run the full multi-tool sweep against a host

Point it at a hostname or IP and let it chain every check.

```
rapidscan target.example.com
```

Outcome: runs roughly 80 vulnerability checks in sequence (port scan, WAF
detection, CMS fingerprinting, SSL weaknesses, DNS zone transfer, subdomain
and directory brute forcing, shallow XSS/SQLi banners, LFI/RFI/RCE probes)
and prints categorized findings with severity legends; long-running tests
are marked so they can be skipped with Ctrl+C.

*No corpus evidence — derived from upstream documentation (skavngr/rapidscan README; packaged as `/usr/bin/rapidscan` wrapping `rapidscan.py`).*
