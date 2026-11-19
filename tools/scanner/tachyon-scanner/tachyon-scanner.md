---
name: "tachyon-scanner"
categories:
  - blackarch-scanner
version: "3.5.6.r4.gba0fe61"
homepage: "https://github.com/delvelabs/tachyon"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-02"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "delvelabs/tachyon"
github-stars: 221
github-pushed: "2026-07-27"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# tachyon-scanner

> Fast web crawler that hunts leftover, non-indexed and leaky files.

## What it's for

Tachyon (Delve Labs; PyPI package `tachyon3`, command `tachyon`, packaged as
`tachyon-scanner`) is web-application reconnaissance: it crawls a site
looking for left-over or non-indexed files and for reporting pages or scripts
that leak internal data. Discovery is plugin-driven — the bundled plugins
derive candidate hosts and filenames from the hostname, generate paths, and
ingest robots.txt, sitemap.xml and `/.svn/entries` into the path database —
with recursive crawling, depth limits, a cookie file for authenticated
crawling, vhost and user-agent overrides, proxy support and JSON output. It
finds exposed material (forgotten backups, export scripts, source-control
leftovers) rather than testing for vulnerabilities.

## Typical use cases

### Run a default discovery

One-shot crawl and probe of a site.

```
tachyon http://target.example.com/
```

Outcome: the site is crawled with default settings and discovered
non-indexed files and potentially leaky paths are reported (on BlackArch the
command is installed as `tachyon-scanner`).

### Restrict to files, with JSON output

Feed downstream tooling.

```
tachyon -f -j http://target.example.com/
```

Outcome: discovery is limited to files (`-s` would limit it to directories)
and results are emitted as JSON for programmatic handling.

### Crawl through a proxy with a session cookie

Inspect authenticated surfaces.

```
tachyon -p http://127.0.0.1:8080 -c cookies.txt -r http://target.example.com/
```

Outcome: all traffic is routed through the intercepting proxy for manual
review, requests carry the cookies from the file (one `name=value;` per
line), and crawling follows links recursively (`-r`).

*No corpus evidence — derived from upstream documentation (delvelabs/tachyon
README usage and command-line options).*
