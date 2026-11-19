---
name: "flunym0us"
categories:
  - blackarch-scanner
version: "2.0"
homepage: "https://code.google.com/archive/p/flunym0us/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-14"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "fluproject/flunym0us"
github-stars: 29
github-pushed: "2017-01-15"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# flunym0us

> Vulnerability-minded content enumerator from the Flu Project team: dictionary-discovers WordPress plugins and Moodle extensions, and pulls WordPress core/plugin versions, path disclosures and author lists.

*No corpus evidence — derived from upstream documentation.*

## What it's for

flunym0us (Python 2, GPLv3, by Juan Antonio Calles, Pablo Gonzalez, Chema Garcia and German Sanchez of the Flu Project) fingerprints content-management systems from the outside. Version 2.0 enumerates WordPress plugins and their versions, the WordPress core version and latest-release comparison, path-disclosure issues and WordPress author accounts; the same dictionary engine covers Moodle extensions. It is multiprocess and multithreaded, with configurable timeouts and retries, and it can spoof the User-Agent and Referer it sends. It enumerates what a site exposes; exploiting what it finds is left to you. The project is archived (its Google Code home is long gone), so expect Python 2 era dependencies.

## Typical use cases

### Enumerate WordPress plugins

```
flunym0us -wp -H target.example.com -w wp-plugins.lst -T 20
```

Outcome: requests each candidate plugin path from the wordlist (the shipped `wp-plugins.lst` and `useragents.py` install alongside the script) with 20 threads per process; discovered plugins print together with any detected versions, plus the WordPress core version and path-disclosure findings when detectable.

### Enumerate Moodle extensions

```
flunym0us -mo -H target.example.com -w moodle-plugins.lst -T 20
```

Outcome: the same dictionary sweep against a Moodle site's extension paths, listing installed modules so their versions can be checked against known advisories. `-p` raises the process count for very large lists, and `-t`/`-r` tune timeout and retries; engine selection (`-wp` vs `-mo`) is mutually exclusive.
