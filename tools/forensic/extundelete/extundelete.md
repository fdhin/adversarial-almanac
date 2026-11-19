---
name: "extundelete"
categories:
  - forensics
  - recover
version: "0.2.4-3+b2"
homepage: "http://extundelete.sourceforge.net/"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali, aur, nixpkgs]
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "cherojeong/extundelete"
github-stars: 54
github-pushed: "2016-06-27"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# extundelete

> Recovers deleted files from an unmounted ext3 or ext4 partition by using the information still stored in the file system journal.

## What it's for

extundelete restores deleted files from ext3/ext4 partitions. It reads the partition's journal to find the metadata state before the deletion, then recovers files to a `RECOVERED_FILES/` subdirectory of the current working directory — the source partition is only ever read. The partition must be unmounted (or mounted read-only) during recovery, and the same password-free rule of all journal-based recovery applies: there is no guarantee any particular file can be undeleted, since the journal may have wrapped or the freed blocks been reused. It is the classic tool for the "just deleted a file I still need" scenario on Linux; for volumes whose metadata is already destroyed, a carver is the better fit.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Restore everything recoverable from a partition

```
extundelete /dev/sda4 --restore-all
```

Outcome: walks the journal and directory structure and writes every file it can recover into `./RECOVERED_FILES/`, keeping original names where possible (unnameable files become `file.NNNN`). Expect a long CPU-bound pause while the directory structure is scanned.

### Restore a single deleted file

```
extundelete /dev/sda4 --restore-file home/user/report.txt
```

Outcome: attempts to restore the file that was deleted at the given path (relative to the file system root) into `./RECOVERED_FILES/home/user/report.txt`. Faster than `--restore-all` when only one file matters.

### Restore a deleted directory

```
extundelete /dev/sda4 --restore-directory home/user/projects
```

Outcome: restores all files it can link to that directory tree into `./RECOVERED_FILES/`.

### Limit recovery to a deletion time window

```
extundelete /dev/sda4 --restore-all --after $(date -d "-2 hours" +%s)
```

Outcome: only restores files whose deletion timestamp is after the given epoch seconds; combine with `--before <epoch>` to bound the other end of the window.

### Inspect the file system before recovering

```
extundelete /dev/sda4 --superblock
```

Outcome: prints file system information from the superblock (`--journal --superblock` for the journal's own superblock, `--inode 2` or `--block 9652` for a specific inode/block), useful to confirm the right partition and a sane journal before running a recovery.
