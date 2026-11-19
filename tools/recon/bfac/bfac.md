---
name: "bfac"
categories:
  - blackarch-recon
version: "v1.0.r12.g18fb0b5"
homepage: "https://github.com/mazen160/bfac"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "mazen160/bfac"
github-stars: 567
github-pushed: "2022-08-25"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bfac

> Check a web app for backup and version-control artifacts that leak its source code.

## What it's for

BFAC (Backup File Artifacts Checker, Mazin Ahmed) probes web applications for backup artifacts — `file.php.bak`, `file.php~`, editor droppings, `.git/` and other DVCS remnants — that expose source code, credentials or directory structure. It generates candidate artifact URLs at several testing levels, then decides which really exist using both status-code checks and a content-length baseline comparison (catching servers that spoof 404s), with rate throttling, proxy support and randomized user agents. It works from the command line or as a Python module and reports in simple, verbose, CSV or JSON form.

## Typical use cases

### Check a single URL

```
bfac --url http://example.com/test.php
```

Outcome: requests the artifact variants of that URL across all testing levels and prints any backup/DVCS artifacts confirmed to exist, with the response evidence for each.

### Check a batch of URLs

```
bfac --list urls.txt
```

Outcome: runs the same artifact checks against every URL in the file — the standard shape for scanning a whole application's page inventory in one pass.

### Tune the testing level

```
bfac --url http://example.com/test.php --level 2
```

Outcome: restricts checks to the artifact patterns of level 2 (lower = faster and quieter, all levels run by default) — trade coverage for speed on large targets.

### Hunt only exposed version-control directories

```
bfac --dvcs-test --url http://example.com/
```

Outcome: limits testing to DVCS exposure (`.git` and friends), skipping file-backup patterns — a focused leak check independent of the general levels.

### Trust content-length detection over status codes

```
bfac --detection-technique content_length --url http://example.com/test.php
bfac --exclude-status-codes 301,404 --url http://example.com/test.php
```

Outcome: the first forces existence decisions to rely on response-size deviation from the invalid-request baseline (defeats status-code spoofing); the second drops results returning the listed status codes, cutting redirect/not-found noise from reports.

*No corpus evidence — derived from upstream documentation.*
