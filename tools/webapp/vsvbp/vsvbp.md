---
name: "vsvbp"
categories:
  - blackarch-webapp
version: "6.241a7ab"
homepage: "https://github.com/varunjammula/VSVBP"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "varunjammula/VSVBP"
github-stars: 7
github-pushed: "2015-11-24"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vsvbp

> Flask web application that crawls a site, indexes its pages, and probes them with SQL-injection and XSS vectors from a browser UI.

## What it's for

VSVBP (Varun Chandra Jammula) is a black-box vulnerability-detection tool for web applications delivered as a small Flask web application. You give it the root link of a website; it crawls the site for all reachable pages, indexes them, and then injects SQL-injection and XSS test vectors against the collected pages to perform its vulnerability analysis. Operation is through the bundled web interface (a login screen and a scanner form) rather than a CLI — the packaged command only starts the Flask development server. It is a 2015-era Python 2 project, so expect a minimal UI and simple signature-driven checks rather than scanner-grade detection logic.

## Typical use cases

### Start the scanner web UI

Bring up the interface and submit a target.

```
vsvbp
```

Outcome: starts the Flask development server (Python 2) on localhost:5000; open http://localhost:5000 in a browser, log in, and submit the target's root URL on the scanner page to kick off the crawl-index-inject analysis.

No corpus evidence — derived from the packaged upstream source (run.py, app/views.py routes of varunjammula/VSVBP).
