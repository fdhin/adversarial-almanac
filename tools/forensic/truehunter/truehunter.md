---
name: "truehunter"
categories:
  - blackarch-forensic
version: "14.0a2895d"
homepage: "https://github.com/adoreste/truehunter"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "adoreste/truehunter"
github-stars: 31
github-pushed: "2021-05-15"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# truehunter

> Heuristic detector for TrueCrypt and VeraCrypt encrypted containers.

## What it's for

truehunter hunts for encrypted containers (TrueCrypt, VeraCrypt, and similar) across a filesystem or mounted image with constant memory use and no external dependencies. It flags files whose size is a multiple of 64 (block-cipher containers), whose first 8 bytes do not match its header database, and — unless fast mode is on — whose entropy looks encrypted. It is a triage tool: hits are candidates for manual inspection, not proof, since any high-entropy file of the right size can match.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Scan a directory tree for containers

Find candidate encrypted volumes on a mounted image.

```
truehunter /mnt/image
```

Outcome: walks the path applying the size, header, and entropy checks; the header database (headers.db) is created on first use, and the findings land in scan_results.csv.

### Fast triage without entropy calculation

Get a quick first pass over a large tree.

```
truehunter -f /mnt/image
```

Outcome: skips the entropy check, flagging every right-sized file with an unknown header — faster, with more false positives to weed out afterwards.

### Constrain the scan window and database

Skip files outside plausible container sizes.

```
truehunter -D headers.db -m 1024 -M 1048576 /mnt/image
```

Outcome: only files between 1 MB (`-m`, value in KB) and 1 GB (`-M`) are considered, using the named header database; `-o results.csv` sends the report to a different file.
