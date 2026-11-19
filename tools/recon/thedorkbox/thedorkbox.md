---
name: "thedorkbox"
categories:
  - blackarch-recon
version: "7.43852d3"
homepage: "https://github.com/cybersafeblr/thedorkbox"
packaged-by: [blackarch]
blackarch-builddate: "2020-04-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "cybersafeblr/thedorkbox"
github-stars: 31
github-pushed: "2019-12-27"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# thedorkbox

> A browsable, categorized collection of Google dorks and OSINT lookup techniques served as a local web page.

## What it's for

TheDorkBox is not a scanner: it is a static HTML reference of Google dorks and OSINT techniques grouped by the kind of confidential data they expose (documents, directories, devices, exposed panels). It exists so that a non-technical investigator can click through categories and run the searches directly in a search engine instead of memorizing dork syntax. The packaged launcher serves the collection over a local PHP web server. It performs no requests against targets itself and produces no findings file — the output is the clickable dork catalog in your browser.

## Typical use cases

### Browse the dork catalog locally

Serve the collection on a local port and open it in a browser.

```
thedorkbox 8000
```

Outcome: a PHP development server listens on 127.0.0.1:8000 serving the dork collection; opening http://127.0.0.1:8000 in a browser shows the categorized dork and OSINT technique pages, each linking to a ready-to-run search-engine query.

### Serve the collection without the wrapper

Run the PHP server directly against the packaged files.

```
php -S 127.0.0.1:8000
```

Outcome: same catalog served from the tool's installed directory (run it from the directory containing the HTML files); useful when the `thedorkbox` wrapper is not on PATH.

*No corpus evidence — derived from upstream documentation.*
