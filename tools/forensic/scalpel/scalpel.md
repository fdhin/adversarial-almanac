---
name: "scalpel"
categories:
  - blackarch-forensic
version: "1.1687261"
homepage: "https://github.com/sleuthkit/scalpel"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2019-01-28"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "sleuthkit/scalpel"
github-stars: 691
github-pushed: "2024-03-27"
github-archived: false
github-issues: 45
github-match: "search"
gh-checked: "2026-11-09"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# scalpel

> Header/footer file carver for disk images — the Foremost-lineage carver, v2.0.

## What it's for

Scalpel carves files out of disk images and raw devices by matching per-type
header/footer signatures from a config file, with no filesystem involved. Its
2.0 generation (based on Foremost 0.69 — see [[foremost]]) added regular
expression headers/footers, embedded header/footer matching, asynchronous I/O
and multithreading. The source lives in the Sleuth Kit GitHub organization
under the Apache 2.0 license and is no longer actively maintained. What gets
carved is decided entirely by `scalpel.conf`, so tuning the config matters;
expect false positives and validate carved output by hand.

## Typical use cases

### Carve files using a config

Recover deleted content from a raw image.

```
scalpel -c scalpel.conf -o carved disk.img
```

Outcome: carves every file type enabled in the config into `carved/` (the
output directory must be new or empty), organized into per-type subdirectories,
and writes an audit log listing everything carved.

### Preview what would be carved

Dry-run before committing to a long carve.

```
scalpel -p -c scalpel.conf disk.img
```

Outcome: carves nothing; the audit log lists exactly which files a real run
would extract — use it to sanity-check config changes.

### Cut false positives on embedded files

Only carve headers at cluster boundaries.

```
scalpel -q -c scalpel.conf -o carved disk.img
```

Outcome: skips headers not aligned to cluster boundaries, dramatically
reducing false positives when embedded files (e.g. pictures inside documents)
are not the target.

### Carve across a list of split images

Process segmented image files in order.

```
scalpel -i files.txt -c scalpel.conf -o carved
```

Outcome: carves from every image file named one per line in `files.txt`,
writing all output into the same `carved/` tree.

### Nested matching for structured types

Catch files embedded in files of the same type.

```
scalpel -e -c scalpel.conf -o carved disk.img
```

Outcome: enables nested header/footer matching so structured file types that
contain embedded files of the same type get carved recursively.

*No corpus evidence — derived from upstream documentation.*
