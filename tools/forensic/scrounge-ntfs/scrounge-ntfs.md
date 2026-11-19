---
name: "scrounge-ntfs"
categories:
  - blackarch-forensic
version: "0.9"
homepage: "http://thewalter.net/stef/software/scrounge/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2015-09-06"
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
github-repo: "lcorbasson/scrounge-ntfs"
github-stars: 12
github-pushed: "2017-02-16"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# scrounge-ntfs

> Small NTFS recovery tool: carve files off a partition given its sector geometry.

## What it's for

scrounge-ntfs (by Stefan Walter) recovers files from NTFS partitions when the
volume's bookkeeping is damaged. It works at the sector level: given the
partition's start and end sectors — or an explicit MFT offset — it parses the
master file table and writes every recoverable file into an output directory.
It is deliberately minimal and manual: no automatic geometry discovery beyond
a partition lister, and the cluster size must be supplied when it differs from
the default of 8 sectors. It is an old, small tool (last release 0.9); for
modern NTFS volumes, fuller-featured recovery suites usually fare better, but
for quick-and-dirty recovery with known geometry it is fast and dependency-free.

## Typical use cases

### List partition geometry

Find the sector numbers scrounge mode needs.

```
scrounge-ntfs -l /dev/sda
```

Outcome: prints each partition on the disk with its start and end sectors —
the values to pass as the trailing arguments in recovery mode.

### Recover all files from a partition

Carve the contents of one NTFS partition to a directory.

```
scrounge-ntfs -o recovered /dev/sda 12345 54321
```

Outcome: parses the NTFS structures between sectors 12345 and 54321 and writes
every recoverable file under the existing directory `recovered/`.

### Supply the MFT offset explicitly

Skip the automatic MFT search when it fails.

```
scrounge-ntfs -m 2081 -c 8 -o recovered /dev/sda 63 20879
```

Outcome: reads the master file table starting at sector 2081 (instead of
hunting for it) using 8-sector clusters, and recovers files into `recovered/`.

*No corpus evidence — derived from upstream documentation.*
