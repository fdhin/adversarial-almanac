---
name: "ddrescue"
categories:
  - forensics
  - recover
version: "1.30-2"
homepage: "https://www.gnu.org/software/ddrescue/ddrescue.html"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-02-24"
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "mruffalo/ddrescue"
github-stars: 48
github-pushed: "2020-03-03"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# ddrescue

> GNU ddrescue — Antonio Diaz Diaz's data recovery tool that copies failing drives into an image while tracking every block in a resumable mapfile.

## What it's for

GNU ddrescue copies data from files, devices or partitions that produce read errors, harvesting everything readable first and relegating bad areas to later passes, so a dying drive is read as few times as possible. Progress is recorded in a mapfile (formerly "logfile") listing every block's status — non-tried, non-trimmed, non-scraped, bad, finished — which makes every run resumable and lets you reason about exactly what was and wasn't recovered. This is the GNU tool (`ddrescue`, by Antonio Diaz Diaz, packaged as `ddrescue` on Arch), not Kurt Garloff's `dd_rescue`, a different project with a similar name; it also ships the companion `ddrescuelog` for inspecting and manipulating mapfiles. It salvages raw blocks — filesystem repair and file carving are separate steps.

## Typical use cases

### Rescue a failing disk into an image with retries

```
ddrescue -r3 /dev/sdb sdb.img sdb.map
```

Outcome: reads the good blocks first, postpones error areas to trimming/scraping passes, and runs three retry passes over what still fails (`-r3`); the mapfile `sdb.map` records per-block status so re-running the same command resumes from where it stopped instead of re-reading the whole drive.

### Two-phase rescue of a partition to a device

```
ddrescue -f -n /dev/sda2 /dev/sdb2 rescue.map
ddrescue -d -f -r3 /dev/sda2 /dev/sdb2 rescue.map
```

Outcome: the first pass copies only non-tried blocks fast (`-n` skips the slow scraping phase); the second reopens with direct I/O (`-d`, bypassing the kernel cache) and retries failures, with `-f` required because the output is a device. Afterwards repair the copy with the filesystem-appropriate fsck before mounting it read-only.

### Review what is still missing

```
ddrescuelog -t sdb.map
```

Outcome: prints a summary of the mapfile — block-status breakdown and how much of the rescue domain is finished — without touching the failing drive, which is the right way to decide whether another retry pass is worth the additional wear.

*No corpus evidence — derived from upstream documentation.*
