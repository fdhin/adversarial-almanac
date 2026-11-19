---
name: "linkfinder"
categories:
  - blackarch-webapp
version: "168.1debac5"
homepage: "https://github.com/GerbenJavado/LinkFinder"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "GerbenJavado/LinkFinder"
github-stars: 4473
github-pushed: "2024-04-13"
github-archived: false
github-issues: 53
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# linkfinder

> Discovers endpoints and their parameters in JavaScript files.

## What it's for

LinkFinder extracts endpoint URLs and their parameters from JavaScript. It pretty-prints the code with jsbeautifier and applies a set of regular expressions that match full URLs (`https://example.com/*`), absolute or dotted paths (`/*`, `../*`), and relative paths with and without slashes. Use it during recon to surface hidden API endpoints and parameters the site crawler never showed — new attack surface for follow-up testing. It only enumerates endpoints; it does not probe or test them. Input can be a single URL, an entire domain, a folder of `.js` files, or a Burp "Save selected items" export; output is an HTML report or plain text.

## Typical use cases

### Extract endpoints from one JavaScript file

```
linkfinder -i https://example.com/static/app.js -o results.html
```

Outcome: fetches the file, prints discovered paths, and writes `results.html` listing every endpoint and parameter found.

### Fast plain-text output to the terminal

Skipping the HTML report also skips jsbeautifier, which is much faster on large files.

```
linkfinder -i https://example.com/static/app.js -o cli
```

Outcome: endpoints print straight to stdout, one per line — pipe them into `sort -u` or a further tool.

### Crawl a whole domain for JS files

```
linkfinder -i https://example.com -d
```

Outcome: enumerates the JavaScript files linked under the domain and reports endpoints found across all of them.

### Mine a Burp saved-items export

Feed a "Save selected items" file captured in Burp to analyze the JS of requests you already proxied.

```
linkfinder -i burp-export.txt -b
```

Outcome: parses every JavaScript body in the Burp export and reports the endpoints it contains.

### Filter to interesting paths across a local folder

```
linkfinder -i 'js/*.js' -r ^/api/ -o api-endpoints.html
```

Outcome: scans all local `.js` files, keeps only endpoints starting with `/api/`, and writes them to `api-endpoints.html`.

No corpus evidence — derived from upstream documentation.
