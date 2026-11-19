---
name: "jsearch"
categories:
  - blackarch-recon
version: "44.87cf9c1"
homepage: "https://github.com/incogbyte/jsearch"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "incogbyte/jsearch"
github-stars: 31
github-pushed: "2021-04-06"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jsearch

> Point it at a web page and it downloads every referenced JavaScript file, then regex-mines them for URLs, internal domains, AWS references and other leakable strings.

## What it's for

Quick client-side recon before touching a target: fetch the page, enumerate its `<script src>` tags, mirror each .js file, and run a pattern set over the contents that surfaces things developers left behind — endpoint URLs, domain names, personal names, AWS/Azure hints, and platform-specific tokens (the bundled patterns flag VTEX e-commerce deployments). Console output color-codes each finding by category and everything downloaded lands in a directory named after the target for manual grep. By v4lak (incogbyte); single-shot tool, no recursion into linked pages (only scripts referenced by the page you give it), needs Python 3.9 or older.

## Typical use cases

### Mine a site's JavaScript for endpoints and secrets

```
python3 jsearch.py -u https://target.example.com -n target
```

Outcome: fetches the page, creates a working directory named after the target (`-n`), downloads every referenced .js file into it, and prints categorized hits as it scans each one — `[INFO URL]`, `[DOMAIN INFO]`, `[AWS INFO]`, `[NAME INFO]`, `[VTEX INFO]` — plus the saved script paths. Grep the created directory for anything the categories missed; the mirrored files are the deliverable for follow-up endpoint brute-forcing.

*No corpus evidence — derived from the upstream source code (README documents only the install and `-h`).*
