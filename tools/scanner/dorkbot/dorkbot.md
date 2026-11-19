---
name: "dorkbot"
categories:
  - blackarch-scanner
version: "437.2b61966"
homepage: "https://github.com/utiso/dorkbot"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-28"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "utiso/dorkbot"
github-stars: 548
github-pushed: "2026-09-14"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 26
gh-activity-checked: "2026-11-09"
---

# dorkbot

> Pipeline that turns Google/Bing/Common Crawl/Wayback queries into a target database and drips each URL through a web vulnerability scanner such as Wapiti.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Automating dork-driven scanning at scale. dorkbot is a modular command-line tool built from two kinds of modules: indexers, which produce lists of target URLs (Google Custom Search API, Bing API, Common Crawl, Wayback, pywb servers, or stdin), and scanners, which run a vulnerability scan (Wapiti, Spectre Scan `scnr`, or Arachni) against each stored target. Every URL lands in a SQLite or PostgreSQL database with scanned-status tracking and page fingerprinting, so large dork result sets can be indexed once, pruned of duplicates and out-of-scope hosts via an internal blocklist, then scanned in batches. JSON vulnerability reports are written per run. It is an orchestration layer — the actual vulnerability detection belongs to the scanner module it drives.

## Typical use cases

### Index targets from a Google Custom Search query

```
dorkbot -i google_api -o key=YOUR_API_KEY -o engine=YOUR_ENGINE_ID -o query="filetype:php inurl:id"
```

Outcome: runs the dork through your Google API credential, stores every returned URL in the target database (dorkbot.db) and exits; nothing is scanned yet, which lets indexing and scanning run as separate scheduled steps.

### Scan everything that has been indexed

```
dorkbot -s wapiti
```

Outcome: takes each unscanned target from the database, drives the Wapiti command-line scanner against it, marks it scanned, and writes a standard JSON vulnerability report into the reports directory.

### Pull historical URLs for one domain from the Wayback Machine

```
dorkbot -i wayback -o domain=example.com
```

Outcome: indexes archive.org crawl data for the domain and its subdomains into the same target database — a source of forgotten endpoints that no longer appear in current search results.

### Feed a prepared URL list directly

```
cat urls.txt | dorkbot -i stdin
```

Outcome: reads one URL per line from stdin and stores them as targets, the simplest way to run an externally generated list through the same scanner/blocklist machinery.

### Keep specific hosts and patterns out of scope

```
dorkbot --add-blocklist-item "host:mail.example.com"
```

Outcome: records the item in the internal blocklist; stored targets matching it (host, IP or `regex:` pattern) are skipped on future runs, and `--external-blocklist` can point at a shared file or database of exclusions.

### Review what is waiting to be scanned

```
dorkbot --list-targets --unscanned-only
```

Outcome: prints the database URLs not yet scanned, with `--show-stats` available for a one-line total/unscanned count — the quick sanity check before starting a long scanning run.
