---
name: "bqm"
categories:
  - blackarch-misc
version: "v1.5.1.r5.ge287280"
homepage: "https://github.com/Acceis/bqm"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-04-29"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "Acceis/bqm"
github-stars: 41
github-pushed: "2026-07-08"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bqm

> Download, deduplicate and merge BloodHound custom-query collections into one file.

*No corpus evidence — derived from upstream documentation.*

## What it's for

BQM (BloodHound Query Merger) is a dependency-free Ruby tool that solves BloodHound's single-customqueries-file limit: BloodHound loads exactly one custom queries JSON, while the community publishes many complementary query collections. BQM indexes the well-known datasets (CompassSecurity, hausec, awsmhacks, porterhau5/BloodHound-Owned, ZephrFish, Certipy and more, referenced in its `data/query-sets.json`), downloads them, removes duplicate queries and merges everything into one output file — optionally folding in your existing `customqueries.json` and local files or directories. Use it to maintain a maximal curated query set instead of committing to one project's file. It only manages query lists; it does not run queries or talk to a BloodHound instance.

## Typical use cases

### List the known query datasets

```
bqm -l
```

Outcome: prints every dataset BQM can fetch (from `data/query-sets.json`) — check coverage before merging, or confirm an entry before adding your own to the file.

### Build the merged custom queries file

```
bqm -o ~/.config/bloodhound/customqueries.json
```

Outcome: downloads all referenced datasets, deduplicates and merges them, and writes the result where the BloodHound GUI loads custom queries. If the output file already exists, BQM asks whether to overwrite it or first merge the existing queries into the new set.

### Include local query files in the merge

```
bqm -o /tmp/customqueries.json -i /tmp/extra.json,/tmp/internal-queries/
```

Outcome: the same fetch-dedup-merge run, additionally folding the listed local files and directories (comma-separated) into the output — combine an internally curated set with the public collections in one file.
