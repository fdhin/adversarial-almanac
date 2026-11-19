---
name: "goop"
categories:
  - blackarch-recon
version: "12.39b34eb"
homepage: "https://github.com/s0md3v/goop"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "s0md3v/goop"
github-stars: 566
github-pushed: "2019-08-23"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# goop

> Google search results as data — a CAPTCHA-free scraper built on Facebook's link debugger.

## What it's for

goop is a proof-of-concept Python library (plus a tiny CLI) that scrapes
Google search results without hitting CAPTCHAs or rate limits, by routing
queries through Facebook's URL debugger endpoint, whose scraper Google
treated as whitelisted. Every request must carry a Facebook session cookie,
so it presumes you already have a logged-in session. It returns structured
results (URL, title, summary) instead of HTML, ready for post-processing.
Be aware of what it is today: the author states Google silently fixed the
bypass and the repository is archived — treat goop as a study in scraping
technique that may no longer return live results, not as a dependable
harvester.

## Typical use cases

### Search Google from the CLI

A Facebook session cookie is hardcoded at the top of the script; edit the
installed `goop` entry point to hold yours before running.

```
goop 'site:example.com filetype:pdf' 3
```

Outcome: prints three pages of results (title, URL, summary) fetched through
the debugger endpoint — one colored block per hit.

### Use it as a library

Pull ranked results straight into Python for post-processing.

```
from goop import goop

results = goop.search('red shoes', '<your facebook cookie>', page='1', full=True)
```

Outcome: returns a dict keyed by rank, each entry carrying `url`, `text`
(title) and `summary`; `page` selects the results page and `full=True`
includes omitted results.

*No corpus evidence — derived from upstream documentation.*
