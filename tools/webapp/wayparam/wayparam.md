---
name: "wayparam"
categories:
  - blackarch-webapp
version: "1.0.0"
homepage: "https://github.com/aleff-github/wayparam"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-23"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "aleff-github/wayparam"
github-stars: 8
github-pushed: "2026-11-01"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 89
gh-activity-checked: "2026-11-09"
---

# wayparam

> Fetch and normalize parameterized historical URLs from Wayback (and optionally Common Crawl) to map an application's old attack surface.

## What it's for

Wayparam queries public web-archive indexes — the Wayback CDX API by default, Common Crawl as an opt-in second source — filters out "boring" static-asset URLs, and normalizes what remains: fragments dropped, hosts and ports canonicalized, parameters sorted, and values masked with a placeholder (`FUZZ` by default). The output is an endpoint list ready to feed fuzzers and bug-hunting pipelines. It is an OSINT tool in the spirit of ParamSpider (explicitly inspired by it, rewritten with async I/O): it never touches the live target. Beyond endpoint collection it can emit historical intelligence — first/last-seen dates, capture counts, parameter prevalence, year/month timelines, host/path topology, and parameter co-occurrence — from the same archive metadata.

## Typical use cases

### Collect endpoints for one domain

Build a parameterized URL inventory from history.

```
wayparam -d example.com
```

Outcome: writes per-domain result files under `results/` containing normalized endpoints such as `http://example.com/page?id=FUZZ&sort=FUZZ`, with static assets filtered out.

### Process a list of domains

Run the collection across a whole scope file.

```
wayparam -l domains.txt
```

Outcome: fetches archive data for every domain in the file concurrently and writes one result file per domain.

### Stream results for piping

Hand the endpoints straight to other tools.

```
wayparam -d example.com --stdout --no-files --format jsonl
```

Outcome: emits JSONL records on stdout and writes nothing to disk; pipe into `jq` or a fuzzer's stdin. Drop `--format jsonl` for plain-text URL lists.

### Tune the filters

Keep only the paths and extensions you care about.

```
wayparam -d example.com --ext-blacklist ".png,.jpg,.css,.js" --exclude-path-regex "^/static/"
```

Outcome: URLs ending in the blacklisted extensions or matching the path regex are dropped before normalization, shrinking the output to potentially interesting endpoints.

### Merge archives with provenance

Combine Wayback and Common Crawl, keeping per-source evidence.

```
wayparam -d example.com --source wayback,commoncrawl --provenance --format jsonl --stdout --no-files
```

Outcome: deduplicated normalized URLs from both indexes; with `--provenance`, one JSONL record per archive source that saw each URL. `--source-summary` instead reports per-source counts, exclusives, and the intersection.

### Diff two historical periods

See what the attack surface looked like then versus now.

```
wayparam -d example.com --changes 2020 2024
```

Outcome: classifies URLs and parameter names observed in each archive bucket as `added`, `removed`, or `persisted` — a hint at forgotten, possibly still-live endpoints (`removed` means absent from the later archive bucket, not proven gone from the site).

*No corpus evidence — derived from upstream documentation (aleff-github/wayparam README).*
