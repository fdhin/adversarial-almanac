---
name: "seat"
categories:
  - blackarch-scanner
version: "0.3"
homepage: "http://thesprawl.org/projects/search-engine-assessment-tool/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "iphelix/seat"
github-stars: 8
github-pushed: "2018-12-04"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# seat

> Gtk2/Perl GUI that mines search engines and cache repositories for vulnerability intelligence about target web sites.

## What it's for

SEAT (Search Engine Assessment Tool, from thesprawl.org, 2009) is a multi-threaded "information digging" application: you load target lists, query packs (including vulnerability databases of crafted search strings) and search-engine definitions into its GUI, and it screen-scrapes the engines in parallel, collecting the results for analysis — simulating what an attacker harvesting public sources would learn about your domains. Search engines (regex signature sets), queries and vulnerability databases are all user-extensible, results can be saved and recycled as new targets, and a proxy is configurable. Two upstream caveats stand: screen scraping will get your network flagged and banned by the engines, and the bundled engine signatures were already stale years ago, so expect to rewrite them before use. The project page is offline; an archived copy documents the GUI workflow. Unrelated to the EVE Online management platform "SeAT" that shares the name.

## Typical use cases

### Launch the GUI and run an assessment

Start a mining session against your own domains.

```
seat
```

Outcome: opens SEAT's Gtk2 interface (the packaged wrapper runs the Perl application from `/usr/share/seat`); the documented workflow then runs Targets (load/save target lists) → Queries (optionally load a vulnerability database) → Execution with thread control watched on the Log screen, and finally Analysis of the mined results, which can be saved or recycled as new targets.

*No corpus evidence — derived from upstream documentation (author's archived GitHub repo iphelix/seat, whose tree matches the packaged 0.3 file set; archived thesprawl.org project page; packaged launcher from the BlackArch recipe; corpus hits for "seat" were OCR garble and the English word in a systemd context).*
