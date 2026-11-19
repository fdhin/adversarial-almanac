---
name: "halberd"
categories:
  - blackarch-scanner
version: "0.2.4"
homepage: "http://halberd.superadditive.com/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: python
license: GPL
meta-checked: "2026-11-09"
github-repo: "jmbr/halberd"
github-match: "exact"
github-stars: 88
github-pushed: "2017-05-11"
github-archived: true
github-issues: 2
gh-checked: "2026-11-09"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# halberd

> Python scanner that unmasks the individual web servers hidden behind an HTTP load balancer by mining differences in their responses.

## What it's for

Halberd (Juan M. Bello Rivas, 0.2.4, packaged from the original PyPI release) discovers how many distinct back-end web servers answer a single URL. It sends many requests, collects per-response clues (timestamps, header and content differences) and groups them to reveal each real server behind a load balancer or round-robin DNS setup. Reach for it during web application audits when the actual back-ends must be addressed directly (bypassing the balancer), or when validating load balancer configuration and session stickiness. It is a discovery tool only — it does not probe for vulnerabilities. Runtime settings live in `~/.halberd/halberd.cfg`.

## Typical use cases

### Discover the web servers behind a load balancer

Point halberd at a target and let it run with defaults.

```
halberd target.example.com
```

Outcome: scans for the configured time, then prints each distinct back-end server found behind the balancer, its address, and the clues that distinguished it.

### Bound a scan's duration and parallelism

Control how long and how aggressively to probe.

```
halberd -t 60 -p 40 target.example.com
```

Outcome: spends at most 60 seconds scanning with 40 parallel threads and prints the same per-server report — useful against slow or fragile targets.

### Batch-scan a list of URLs and write a report

Feed a target list from a file and capture results.

```
halberd -u urls.txt -o report.txt
```

Outcome: scans each URL listed in `urls.txt` (one per line) and writes the consolidated report to `report.txt` in addition to the console output.

### Save the collected clues for later analysis

Persist the evidence used to split servers apart.

```
halberd -w clues/ target.example.com
```

Outcome: writes the collected clue files into the `clues/` directory; reload them later with `-r clues/` to resume or re-analyze without re-scanning.

*No corpus evidence — derived from upstream documentation. The author's own repo is
jmbr/halberd on GitHub (archived March 2023); development moved to
git.superadditive.com/jmbr/halberd.*
