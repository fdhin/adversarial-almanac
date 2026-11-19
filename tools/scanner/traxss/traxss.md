---
name: "traxss"
categories:
  - blackarch-scanner
version: "81.48dee2e"
homepage: "https://github.com/M4cs/traxss"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-29"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "M4cs/traxss"
github-stars: 183
github-pushed: "2023-05-22"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# traxss

> Interactive XSS scanner that tests URL parameters with headless Chrome.

## What it's for

Traxss is a Python 3 framework that scans URLs and webpages for reflected XSS: it gathers query-parameter URLs, injects a payload set (575+ payloads in the full mode, 7 attack vectors in fast mode) and verifies execution with headless Chrome via Selenium, optionally also rendering the page HTML for manual XSS hunting (a beta feature). Operation is entirely menu-driven — launch it, enter the target URL, pick a scan type. A chromedriver binary matching your installed Chrome is required, and execution is browser-based rather than raw HTTP, so it is slower than script-based fuzzers. BlackArch ships it patched for Selenium 4 behind a `traxss` launcher. Suited to quick single-target triage, not authenticated or large-scope crawling.

## Typical use cases

### Start an interactive XSS scan

```bash
traxss
```

Outcome: opens the interactive CLI; enter the target URL and choose a scan mode — Full w/ HTML runs all 575+ payloads plus rendered-HTML analysis, Fast w/o HTML runs only the 7 core vectors — and reflected XSS findings are reported when the run completes.

*No corpus evidence — derived from upstream documentation (M4cs/traxss README scan-type descriptions; BlackArch PKGBUILD wraps `traxss.py` as `traxss` with a selenium 4 migration patch).*
