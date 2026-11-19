---
name: "navgix"
categories:
  - blackarch-scanner
version: "3.6f25c98"
homepage: "https://github.com/hakaioffsec/navgix"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-29"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "hakaioffsec/navgix"
github-stars: 75
github-pushed: "2023-05-20"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# navgix

> Multi-threaded checker for the nginx alias "off-by-slash" directory traversal misconfiguration.

## What it's for

navgix tests nginx sites for the alias traversal bug: a `location /static { alias /.../static/; }` block whose alias ends in a slash lets `/static../` lop the last path segment off and read files outside the aliased directory. It finds candidate directories two ways — heuristically, by parsing the page HTML for resource paths (src attributes) and testing every directory component of each, and by brute-forcing a short built-in list of common ones (static, js, css, assets, images, img, media, lib). It reports which paths are vulnerable; it does not fetch or exfiltrate files itself, so confirm findings with a manual traversal request. Single URL or a file of URLs, with per-site and cross-site concurrency controls.

No corpus evidence — derived from upstream documentation and the tool's source.

## Typical use cases

### Scan a single site

Check one nginx host for alias traversal.

```
navgix scan -u https://target.example.com
```

Outcome: fetches the page, derives candidate directories from its resources plus the built-in common list, tests each for the off-by-slash condition, and prints any vulnerable paths found.

### Scan many sites with tuned concurrency

Work through a URL list faster.

```
navgix scan -u urls.txt -t 20 -c 50
```

Outcome: reads one URL per line and scans up to 50 sites concurrently with 20 threads per site (defaults 10 and 20), reporting vulnerable paths grouped by site.
