---
name: "mantra"
categories:
  - blackarch-scanner
version: "v3.1.r5.g6026816"
homepage: "https://github.com/brosck/mantra"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-03-14"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "brosck/mantra"
github-stars: 947
github-pushed: "2026-03-12"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-09"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# mantra

> Hunt down leaked API keys in JavaScript files and web pages.

## What it's for

Mantra (Go) takes URLs on stdin, fetches each page and its script files, and matches the source against patterns for known API-key formats — cloud and SaaS tokens — plus any custom regex you supply. Hits print as one `[+] <url> [<match>]` line each. Built for checking whether a site leaks third-party API credentials, whether as a bug-bounty sweep or to audit your own applications; it only inspects the delivered source (no DOM execution), so keys constructed at runtime in the browser stay invisible to it.

## Typical use cases

### Scan a list of pages

Sweep a URL list for known key formats.

```
cat urls.txt | mantra
```

Outcome: fetches each URL (50 threads by default) and prints `[+] <url> [<key>]` for every detected API-key leak; URLs are read line-by-line from stdin, so any tool that emits URLs can feed it.

### Add a custom secret pattern

Sweep for an internal token format too.

```
cat urls.txt | mantra -ep 'AIza[0-9A-Za-z_-]{35}' -t 100
```

Outcome: additionally reports matches for the custom regex (`-ep`) with the thread count raised to 100 — any distinctive token format your organization uses can be checked this way.

### Scan behind authentication

Check authenticated app pages.

```
cat urls.txt | mantra -c 'Cookie: session=<value>'
```

Outcome: sends the supplied cookie string with every request so pages and scripts behind the login are fetched and checked as well; `-s` silences the banner for pipeline use.
