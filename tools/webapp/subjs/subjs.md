---
name: "subjs"
categories:
  - blackarch-webapp
version: "48.3eb4dc9"
homepage: "https://github.com/lc/subjs"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-07-26"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "lc/subjs"
github-stars: 866
github-pushed: "2025-07-22"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# subjs

> Fetches JavaScript files from a list of URLs or subdomains for endpoint and secret hunting.

## What it's for

subjs (lc) takes a list of URLs or subdomains and fetches the JavaScript files referenced
by each page, printing the JS URLs to stdout. Analyzing those files helps you find
undocumented endpoints, secrets, and more. It is intentionally a fetcher/listing tool: it
collects the JS URLs (and their contents' location), leaving analysis to a companion tool
such as LinkFinder. It pairs naturally with gau — pipe gau's archived-URL output straight
in. It does not itself extract secrets or parse the scripts.

## Typical use cases

### Collect JS URLs from a URL list

Point it at a file of URLs gathered during recon.

```
subjs -i urls.txt
```

Outcome: every JavaScript file URL found on the given pages is printed to stdout, ready to feed into a downloader or LinkFinder for endpoint/secret extraction.

### Pipe URLs through it in a recon chain

Chain subdomain and archive tooling directly into subjs.

```
cat hosts.txt | gau | subjs -c 40
```

Outcome: archived URLs for each host are fetched and their JS file URLs printed, with 40 concurrent workers (`-c`) for speed; timeout and user-agent are settable with `-t` and `-ua`.

No corpus evidence — derived from upstream documentation.
