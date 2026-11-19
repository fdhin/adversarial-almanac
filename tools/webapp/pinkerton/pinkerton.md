---
name: "pinkerton"
categories:
  - blackarch-webapp
version: "1.7.r13.g6deb355"
homepage: "https://github.com/dsssssssm/Pinkerton"
packaged-by: [blackarch]
blackarch-builddate: "2026-04-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "dsssssssm/Pinkerton"
github-stars: 439
github-pushed: "2026-04-10"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
---

# pinkerton

> Crawls a site for JavaScript files and hunts API keys, tokens and other hardcoded secrets inside them.

## What it's for

Secret-hunting during recon: give it a URL and it crawls the site, collects the JavaScript files it references, and matches their contents against regex patterns (borrowed from SecretFinder/GitLeaks-style sets) for API keys, authorization tokens, hardcoded passwords and similar leaks. It works on three input shapes — a whole site crawl, a single remote `.js` URL, or a file already on disk. It only reads what the pages expose; it is not a general-purpose crawler or a vulnerability scanner. Upstream runs it as `python3 main.py` from the repo; the BlackArch package installs a `pinkerton` wrapper (the project has moved between GitHub accounts, most recently to `dsssssssm/Pinkerton`).

## Typical use cases

### Crawl a site and scan every JavaScript file found

Point it at a root URL and let it gather the JS.

```
pinkerton -u https://example.com
```

Outcome: crawls the site, downloads each discovered JavaScript file and prints any secret-pattern matches (API keys, tokens, passwords) with the file they came from.

### Scan a single remote JS file

Skip the crawl when you already have a suspect script URL.

```
pinkerton -j https://example.com/static/app.js
```

Outcome: fetches just that file and reports regex matches — quick triage of a script spotted in a page's source.

### Audit a local JavaScript file

Check a file you already pulled down (e.g. from a client engagement bundle).

```
pinkerton -f app.min.js
```

Outcome: runs the same secret patterns over the local file and prints findings, with no requests sent to the original site.

### Crawl with an authenticated session header

Reach JS that is only served to logged-in users.

```
pinkerton -H Authorization "Bearer <token>" -u https://example.com
```

Outcome: every crawl request carries the repeated custom header (`-H Name Value`, usable multiple times), so protected pages and their scripts are fetched and scanned too.

*Provenance: no corpus evidence — derived from upstream documentation (README + `main.py` argparse, verified 2026-11).*
