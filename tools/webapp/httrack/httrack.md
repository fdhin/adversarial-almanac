---
name: "httrack"
categories:
  - web
version: "3.50.5-1"
homepage: "https://www.httrack.com/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-04"
build-system: autotools
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "xroche/httrack"
github-stars: 4827
github-pushed: "2026-11-07"
github-archived: false
github-issues: 8
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1460
gh-activity-checked: "2026-11-09"
---

# httrack

> Copies websites to a local directory, rebuilding the original link structure so the mirror can be browsed offline.

## What it's for

HTTrack is the classic offline browser: it downloads a website — HTML, images, and whatever file types it is told to mirror — recursively, arranges the relative link structure, and lets you browse the copy locally as if you were online. It can update an existing mirror incrementally (`--update`), resume interrupted transfers (`--continue`), and constrain runs with include/exclude filters, depth limits, rate limits, proxies and authentication. It is a retrieval tool, not a security scanner, though it is routinely used to snapshot a target's client-visible surface for offline review. GUI wrappers (WinHTTrack, WebHTTrack) sit on top of the same `httrack` engine documented here.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Mirror a whole site

The basic full-site copy.

```
httrack https://www.example.com/
```

Outcome: recursively mirrors the site into a project directory under the current working directory, rewriting the internal links so every page browses correctly offline.

### Mirror to an explicit path with scope and depth bounds

Control where it lands and how far it wanders.

```
httrack https://www.example.com/ -O mirror "+*.example.com/*" -r4
```

Outcome: the mirror is written under `mirror/`, crawling is limited to depth 4 (`-rN`) and restricted to example.com hosts by the include filter — other domains are ignored.

### Update or resume a mirror

Refresh a copy without refetching everything.

```
httrack --update
```

Outcome: re-runs the most recent mirror in the current project directory, transferring only new and changed files; `--continue` instead resumes an interrupted pass from where it stopped.

### Spider without saving, through a proxy

Walk links for discovery while routing traffic elsewhere.

```
httrack https://www.example.com/bob/bobby.html --spider -P proxy.example.com:8080
```

Outcome: crawls links from the page without storing files (spider mode), with all transfers routed through the given proxy.
