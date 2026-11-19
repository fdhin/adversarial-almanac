---
name: "waybackpack"
categories:
  - blackarch-webapp
version: "v0.6.4.r1.gc5507a0"
homepage: "https://github.com/jsvine/waybackpack"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-17"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "jsvine/waybackpack"
github-stars: 3241
github-pushed: "2025-04-21"
github-archived: false
github-issues: 33
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# waybackpack

> Command-line downloader for the Wayback Machine archive of a given URL.

## What it's for

Waybackpack fetches every snapshot the Internet Archive holds for a URL, either writing all captures to disk in timestamped directories or just listing them. It bounds downloads by date, collapses duplicates, throttles with delays and retries, and can fetch snapshots in their original raw bytes. It is pure Python on top of `requests`. Use it for OSINT and change-history on a site; it can only retrieve what the archive actually captured — never-archived pages are invisible to it.

## Typical use cases

### Download every snapshot of a page

Pull the full archive history into a local directory.

```
waybackpack http://example.com/ -d ~/Downloads/example-wayback
```

Outcome: creates the directory and writes each capture under a timestamped path (`<timestamp>/<host>/<path>`), one directory per archived moment.

### Bound the download to a date range

Only fetch captures from a period of interest.

```
waybackpack http://example.com/ -d ~/Downloads/example-wayback --from-date 2019 --to-date 2020
```

Outcome: only snapshots whose timestamps fall in the range are downloaded; dates are `YYYYMMDDhhss` strings with trailing digits optional, so `2019` means all of 2019.

### List snapshots without downloading

See what the archive holds before committing to a download.

```
waybackpack http://example.com/ --list
```

Outcome: prints the URL of each archived snapshot, one per line, and writes nothing to disk.

### Collapse duplicates and stay polite

Fetch a representative sample instead of every capture.

```
waybackpack http://example.com/ -d out --collapse timestamp:6 --uniques-only --no-clobber
```

Outcome: keeps at most one capture per month (`timestamp:6` is an archive.org collapse parameter), downloads only the first version of duplicate files, and skips files already present — far fewer requests against the archive.

### Fetch original unprocessed bytes

Get captures exactly as recorded, without Wayback rewriting.

```
waybackpack http://example.com/ -d out --raw
```

Outcome: each snapshot is fetched in its original state with no processing by the Wayback Machine or waybackpack, which matters when the rewritten version alters headers or content.

*No corpus evidence — derived from upstream documentation (jsvine/waybackpack README).*
