---
name: "xsspy"
categories:
  - blackarch-webapp
version: "60.b10d336"
homepage: "https://github.com/faizann24/XssPy"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "faizann24/XssPy"
github-stars: 835
github-pushed: "2023-01-20"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xsspy

> Menu-driven Python crawler that maps a site's links and subdomains, then probes every discovered input for reflected XSS.

## What it's for

XssPy (by Faizan Ahmad, Fsecurify) is a web-application XSS scanner that works in two phases: it first traverses the target website, collecting links and subdomains, and then scans each input on every page it found using small but effective payloads. Unlike single-page checkers, its coverage follows the crawl; the interactive menu offers a short scan, a comprehensive scan and subdomain discovery. It is a Python 2/mechanize-era tool (the distro package installs `XssPy.py` as the `xsspy` command), takes the bare domain without `http://` or `www`, and reports the parameters where a payload reflected.

## Typical use cases

### Run a full scan against a site

```
xsspy example.com
```

Outcome: the crawler enumerates links and subdomains under `example.com`, then the menu lets you pick a short or comprehensive scan; every input found is tested with the built-in payload set and reflective hits are printed as they are found.

*No corpus evidence — derived from upstream documentation (README of faizann24/XssPy).*
