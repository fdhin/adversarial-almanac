---
name: "second-order"
categories:
  - blackarch-webapp
version: "v3.2.r12.g400a81d"
homepage: "https://github.com/mhmdiaa/second-order"
packaged-by: [blackarch]
blackarch-builddate: "2025-06-14"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "mhmdiaa/second-order"
github-stars: 407
github-pushed: "2026-03-29"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# second-order

> Crawler that hunts second-order subdomain takeover by logging external URLs, attributes and inline content a web app references.

## What it's for

Second Order (by mhmdiaa) addresses a blind spot of ordinary takeover
scanners: references the app itself makes to external, possibly abandoned
destinations — script/image `src` attributes, `href` links, CDN paths —
which you only see after crawling the rendered app. It crawls a target to a
configurable depth and, driven by a JSON config, records every tag/attribute
combination you care about, optionally flags those whose URL does not return
HTTP 200 (the takeover-shaped ones), and saves everything as JSON keyed by
page. The takeover detection itself is you checking the collected URLs
against takeover-able services; the shipped `config/takeover.json` is tuned
for that, but the same engine doubles as a JS collector (`javascript.json`),
static-host finder (`cdn.json`) and input-name harvester
(`parameters.json`) for parameter wordlists. It does not send any exploit
traffic, only GETs while crawling.

## Typical use cases

### Scan for second-order subdomain takeover

Crawl with the takeover-tuned config.

```
second-order -t https://target.example.com -c takeover.json
```

Outcome: crawls the app, collects external URLs from tag attributes and
writes `non-200-url-attributes.json`, `attributes.json` and `inline.json`
under `output/` — every entry in the non-200 file pointing at a
takeover-able service (dangling S3, GitHub Pages, Heroku, ...) is a finding.

### Harvest all JavaScript a site loads

Use the javascript config to inventory JS surface.

```
second-order -t https://target.example.com -c javascript.json -depth 2 -threads 20
```

Outcome: crawls two levels deep with 20 threads and logs inline and imported
JS per page — the input list for secret/endpoint hunting in those files.

### Collect input names for parameter brute forcing

Build a tailored parameter wordlist from the app's own forms.

```
second-order -t https://target.example.com -c parameters.json
```

Outcome: records every `input[name]` value per crawled page in
`attributes.json`; deduplicated, that list is a high-hit-rate parameter
wordlist for fuzzers.

*No corpus evidence — derived from upstream documentation (mhmdiaa/second-order README options, config semantics and output format).*
