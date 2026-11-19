---
name: "linkedint"
categories:
  - blackarch-social
version: "1.1.FIX.r8.g89ab1d3"
homepage: "https://github.com/vysecurity/LinkedInt"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "vysecurity/LinkedInt"
github-stars: 1217
github-pushed: "2023-03-06"
github-archived: true
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# linkedint

> Interactive LinkedIn employee harvester with email prediction and an HTML report.

## What it's for

LinkedInt (Vincent Yiu; repository archived upstream) searches LinkedIn with your own credentials, filters results to a target company, and produces an offline-viewable HTML report of matching employees with embedded profile photos. Alongside the people data it predicts the company's email address format and generates candidate addresses per employee, optionally using a Hunter.io API key to pick the correct prefix automatically. Everything happens through interactive prompts — there is no flag surface. Expect breakage: the upstream scraper depends on LinkedIn's then-current page structure and has not been maintained since 2021.

## Typical use cases

### Harvest a company's employees interactively

Put your LinkedIn username/password (and optionally a Hunter.io API key) into `LinkedInt.cfg`, then run.

```
python3 LinkedInt.py
```

Outcome: a prompt-driven session asks for search keywords, an output filename, company filtering, the target's email domain, and the address prefix scheme; it enumerates matching employees, predicts addresses like `first.last@targetco.com`, and writes an HTML report with embedded photos that opens offline.

*No corpus evidence — derived from upstream documentation.*
