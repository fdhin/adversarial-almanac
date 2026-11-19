---
name: "ext4magic"
categories:
  - blackarch-forensic
version: "0.3.2"
homepage: "https://sourceforge.net/projects/ext4magic/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2025-10-12"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "gktrk/ext4magic"
github-stars: 24
github-pushed: "2024-01-08"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ext4magic

> Recovers deleted files from ext3/ext4 file systems by replaying old inode and directory-block copies found in the file system journal.

## What it's for

Deleting a file on ext3/ext4 zeroes the block references in its inode, which plain metadata tools cannot undo — but the journal usually still holds earlier copies of that inode and of the directory blocks. ext4magic extracts those copies and rebuilds files and whole directory trees, preserving original file names, owner, group, mode bits and timestamps where the journal data suffices. It can also inspect a file system (superblock, journal header, timestamp histograms, journal transaction lists) and list a directory as it was at a past time. The file system must be unmounted or mounted read-only, and recovery output goes to a separate target directory — the source is never written to. It cannot help once the journal has wrapped past the deletion or the metadata is destroyed; a carving tool is the fallback then.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Recover a just-deleted file by path

```
ext4magic /dev/sda3 -r -f user1/picture/photo.jpg -d /tmp/recovered
```

Outcome: restores the deleted file to `RECOVERED` output under `/tmp/recovered/user1/picture/photo.jpg`. Note that `-f` takes the path from the root of the file system, not of the Linux tree (for a volume mounted at `/home`, `/home/user1/...` becomes `user1/...`).

### Recover everything deleted in the last 24 hours

```
ext4magic /dev/sda3 -r
```

Outcome: attempts to restore all files deleted within the default 24-hour window and writes them into `./RECOVERDIR/`.

### Recover a directory tree inside a time window

```
ext4magic /dev/sda3 -R -f user1/documents -a $(date -d "-5 days" +%s) -b $(date -d "-4 days" +%s) -d /mnt/recovery
```

Outcome: `-R` recursively recovers the `user1/documents` tree; `-a` (after) bounds how old a version may be and `-b` (before) is set to just before the deletion, so only inode copies from that window are used. Recovered files land under `/mnt/recovery`.

### Full multi-stage recovery after a recursive delete

```
ext4magic /dev/sda3 -M -d /mnt/recovery
```

Outcome: runs the multi-stage "magic" recovery intended when the whole file system was deleted (e.g. an `rm -rf` at its root); `-m` instead recovers only the deleted files of a still mostly intact file system.

### List what a directory held at a past time

```
ext4magic /home/filesystem.iso -Lx -f user1
```

Outcome: prints the historic content of `user1/` from journal copies of its directory blocks (`-x` adds detail), which is useful to enumerate candidates before a targeted `-r`/`-R` recovery run.
