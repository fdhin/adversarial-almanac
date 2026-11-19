---
name: "dirble"
categories:
  - blackarch-webapp
version: "1.4.2"
homepage: "https://github.com/nccgroup/dirble"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-07-11"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nccgroup/dirble"
github-stars: 631
github-pushed: "2026-04-14"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dirble

> Fast directory and file scanner with recursion and listable-directory scraping.

## What it's for

Dirble brute-forces web paths for directories and files, reporting response code and size per find. Beyond plain scanning it detects and scrapes listable "Index Of" directories for their linked URLs, recursively scans discovered directories, throttles requests, and handles cookies, custom headers, basic auth and proxies, with a threading model built on jobs and split wordlists. Written in Rust. It is a discovery tool only — it reports what responds, not why it is interesting.

## Typical use cases

### Scan with the default wordlist

```
dirble https://example.com
```

Outcome: scans the target with the bundled dirble_wordlist.txt, printing each find as `+ <url> (CODE:...|SIZE:...)` and recursively following discovered directories.

### Use a custom wordlist with extensions

```
dirble https://example.com -w example_wordlist.txt -x .php,.html
```

Outcome: substitutes your wordlist and appends each listed extension to every entry, catching the .php/.html files the bare list misses.

### Scrape listable directories

```
dirble https://example.com --scrape-listable
```

Outcome: when a directory serves an index listing, parses every linked URL out of it and adds them to the scan queue — the full content of "Index Of" pages without brute force.

### Scan many hosts in one run

```
dirble https://example.com -X wordlists/web.lst -U hostlist.txt
```

Outcome: applies the extension list in web.lst across every host in hostlist.txt in a single invocation.

### Tune threading and disable recursion

```
dirble https://example.com --max-threads 10 --wordlist-split 10 -r
```

Outcome: runs gobuster-style — ten threads over the main directory only, no recursion into subdirectories.

_No corpus evidence — derived from upstream documentation (README examples)._
