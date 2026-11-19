---
name: "page-fetch"
categories:
  - recon
version: "git (2026-07-03)"
homepage: "https://github.com/detectify/page-fetch"
origin: "github"
recipe: "upstream"
packaged-by: []
build-system: go
meta-checked: "2026-11-09"
github-repo: "detectify/page-fetch"
github-stars: 529
github-pushed: "2026-07-03"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 6
---

# page-fetch

> Fetch web pages with headless Chrome and store the complete resource tree — then run your own JavaScript across many pages and collect the return values.

## What it's for

page-fetch (Detectify, Go + chromedp) is a batch fetcher for researchers: it takes a list of URLs on stdin, loads each in headless Chrome so JavaScript-dependent content actually renders, and saves every fetched resource — HTML, scripts, CSS, images — into an output directory that mirrors the target's own path structure, with a `.meta` file per request recording the original URL and full request/response headers. Beyond mirroring, its distinguishing feature is `-j/--javascript`: arbitrary JS executed on every page, whose return value is printed on lines prefixed `JS`, turning a URL list into a distributed extraction primitive. It needs a local Chrome/Chromium (it probes the usual executable names) and is a fetching/measuring tool only — it does not crawl or parse for you; what you fetch and extract is up to you.

## Typical use cases

### Mirror a site's full resource tree

Fetch one or many URLs and keep everything, including the JavaScript.

```
echo https://example.com | page-fetch
```

Outcome: each request is logged as it completes (`GET https://example.com/ 200 text/html`), and all responses land under `out/` mirroring the site's directory layout (`out/example.com/index` plus `index.meta` with headers) — ready for offline review of what the page actually loaded.

### Save only the content types you care about

Skip images or keep only HTML across a large run.

```
page-fetch --include text/html < urls.txt
page-fetch --exclude image/ < urls.txt
```

Outcome: with `--include` only matching content types are stored; with `--exclude` matching ones are dropped — both accept multiple flags — keeping the output tree small when you only want documents or scripts.

### Extract data from every page with JavaScript

Run one JS snippet across the whole URL list and read the values.

```
echo https://example.com | page-fetch --javascript '[...document.querySelectorAll("a")].map(n => n.href)' | grep ^JS
```

Outcome: for each page, the snippet's return value is stringified and printed as `JS (<url>): <value>` — here the href of every link, one page per line, ready to pipe into `grep`/`sort`/further processing.

### Fetch through a proxy

Route all headless-Chrome traffic through an intercepting proxy.

```
echo https://example.com | page-fetch --proxy http://localhost:8080
```

Outcome: every request goes through the given proxy (e.g. Burp Suite on its default port), so the rendered traffic appears in the proxy for inspection while still being stored in the output directory.

> No corpus evidence — derived from upstream documentation.
