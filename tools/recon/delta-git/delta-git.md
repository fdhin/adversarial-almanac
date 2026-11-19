---
name: "delta-git"
categories:
  - recon
version: "20160608.r5"
homepage: "https://github.com/pangbo-1988/data_diff"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, nixpkgs]
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "pangbo-1988/data_diff"
github-stars: 6
github-pushed: "2016-06-09"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# delta-git

> Diff two crawl snapshots of a website to see exactly what appeared, disappeared or stayed.

## What it's for

delta (the `data_diff` project) tracks changes in data sets over time, with
website monitoring as its headline use case: crawl a site with any tool that
emits JSON (a Scrapy spider is the documented example), ingest each snapshot
under a source name, and later compute set differences between any two stored
snapshots — newly added URLs, removed endpoints, or the overlap. Storage
lives in Elasticsearch (host and index names configured in `config.py`, which
must point at a reachable server before anything works), and record identity
is decided by the key fields named there. It is a Python 2 era tool (2016,
unmaintained since) and despite sitting in DNS-heavy company it has nothing
to do with DNS — it diffs arbitrary JSON data sets.

## Typical use cases

### Ingest two crawl snapshots of the same site

Store "before" and "after" for later comparison.

```
python2 delta.py -p crawl_v1.json website_a
python2 delta.py -p crawl_v2.json website_a
```

Outcome: each run loads one JSON snapshot into the Elasticsearch backend
under source name `website_a`, auto-incrementing its version number; nothing
is printed on success, so follow up with `-l` to confirm both versions are
registered.

### List stored data sets and their version hashes

Find the two snapshot hashes you want to compare.

```
python2 delta.py -l
```

Outcome: prints a table of key (content hash), source name, version and
ingestion time for every stored data set — copy the two hashes that
correspond to your before/after crawls.

### Show what changed between two snapshots

Compute the set difference; argument order decides the direction.

```
python2 delta.py -m HASH_V2 HASH_V1
```

Outcome: prints the JSON records present in the first data set but absent
from the second — running it as (v2, v1) surfaces what was added between the
crawls, and as (v1, v2) what was removed. The sibling options do the other
set operations: `-o HASH1 HASH2` for the overlap and `-c HASH1 HASH2` for the
combined union; `-D` wipes all stored data sets.

No corpus evidence — derived from upstream documentation (README and
`delta.py` option parser).
