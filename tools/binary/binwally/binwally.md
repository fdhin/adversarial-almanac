---
name: "binwally"
categories:
  - blackarch-binary
version: "4.0aabd8b"
homepage: "https://github.com/bmaia/binwally"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "bmaia/binwally"
github-stars: 84
github-pushed: "2015-02-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# binwally

> Directory-tree comparison with ssdeep fuzzy hashing: identical files, partial matches with a similarity score, and files unique to either side.

## What it's for

binwally walks two directory trees and scores corresponding files with ssdeep fuzzy hashes instead of exact hashes, so a file that was only partially modified still shows up as a partial match with a 0–100 score rather than as completely different. That is what you want when comparing firmware extractions, vendor update trees, or sample sets where most files carry over with small edits; for plain duplicate detection an exact-hash comparison is enough and cheaper. It is a small Python 2-era script (packaged as the `binwally` command; upstream documents running it as `python binwally.py dir1 dir2`) and needs the python-ssdeep bindings with libfuzzy on the system.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Compare two firmware extractions

```
binwally extracted_v1 extracted_v2
```

Outcome: prints a `SCORE RESULT PATH` report — `100 matches` for identical files, a 0–99 score plus `differs` for partially similar ones, and `<<< unique` / `>>> unique` lines for files present on only one side — followed by the total number of files compared and an overall match percentage; the partial-match set is where the newer image actually changed code.
