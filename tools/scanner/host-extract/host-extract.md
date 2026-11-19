---
name: "host-extract"
categories:
  - blackarch-scanner
version: "8.0134ad7"
homepage: "https://code.google.com/p/host-extract/"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "jeffrbeyer/host-extract"
github-stars: 0
github-pushed: "2015-08-10"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# host-extract

> Ruby script that mines IP addresses, internal hostnames, dev hosts and CDN endpoints out of a page's HTML, comments and inline scripts, plus its linked JavaScript and CSS files.

## What it's for

host-extract (Aung Khant, YGN Ethical Hacker Group) widens host discovery beyond what crawlers see: instead of following anchor tags it greps host/IP patterns from the full page response — HTML comments, inline scripts and the contents of linked JS/CSS files — surfacing internal IPs, development hosts, CDNs and load balancers that leak in client-side code. It is designed to be fed URL lists produced by your web crawler. Expect occasional false positives (version-like strings in file names); the `-v` view-source snippets make each finding fast to verify by hand.

## Typical use cases

### Extract every host pattern from a page

```
host-extract -a -v https://target.example.com/
```

Outcome: prints every IP/host pattern found in the page response, each with the surrounding source snippet (`-v`) so you can judge whether it is a real infrastructure leak.

### Mine linked JavaScript and CSS assets

```
host-extract -j -c -v https://target.example.com/
```

Outcome: fetches and scans all linked JS (`-j`) and CSS (`-c`) files as well, catching endpoints that appear only in client-side code.

### Batch over a crawler's URL list

```
for u in $(cat url-list); do host-extract -a -v "$u"; done
```

Outcome: repeats the full-pattern extraction for every URL in `url-list`, aggregating internal and external hosts across the whole crawled surface.

*No corpus evidence — derived from upstream documentation.*
