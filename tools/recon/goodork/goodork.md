---
name: "goodork"
categories:
  - blackarch-recon
version: "2.2"
homepage: "http://goo-dork.blogspot.com/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "k3170makan/GooDork"
github-stars: 144
github-pushed: "2013-06-08"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# goodork

> 2013-era Python 2 script that runs Google dorks from the command line and post-filters the results with your own regular expressions.

## What it's for

GooDork combines dorking with regular expressions: it fetches Google results for a search query and lets you apply regex patterns to specific attributes of each result — the URL, the displayable text, the title, the anchors, or the script tags. This turns a plain dork into a targeted filter, e.g. every `.edu` page whose text mentions a keyword, or PHP URLs with numeric parameters hinting at injectable parameters. It is a Python 2 tool (BeautifulSoup-based scraper) last updated in 2013 — modern Google serves bot walls and changed markup, so expect it to need repair before it returns usable results; treat it as a legacy reference implementation of dork-plus-regex filtering.

## Typical use cases

### Filter dork results by page text

```
goodork site:example.com -b "internal use only"
```

Outcome: scrapes Google results for the dork, then prints only results whose displayable text matches the regex — the `-u` (URLs), `-t` (titles), `-a` (anchors), and `-s` (script tags) switches filter on those attributes instead.

### Save matches to a file

```
goodork site:example.com -u "\.php\?.*id=" -o results.txt
```

Outcome: writes the matching URLs to `results.txt` (console output otherwise), here narrowed to PHP pages with an `id=` style parameter — a starter list for injection testing. `-L 100` would cap processing to the first 100 results and `-U "Mozilla"` sets a custom User-Agent.

*No corpus evidence — derived from upstream documentation.*
