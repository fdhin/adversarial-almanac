---
name: "ext3grep"
categories:
  - forensics
  - recover
version: "0.10.2-7"
homepage: "https://code.google.com/archive/p/ext3grep/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2024-07-12"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "meermanr/ext3grep"
github-stars: 18
github-pushed: "2012-02-11"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ext3grep

> Recover deleted files from ext3 filesystems by mining directory entries, inodes, and the journal.

## What it's for

ext3grep (Carlo Wood) investigates an ext3 filesystem — an unmounted
partition device or a raw image of one — for deleted files. Because
ext3 zeroes a deleted inode's block pointers, undeletion is
reconstruction rather than lookup: the tool walks directory blocks for
deleted entries, cross-references inodes and allocation state, and
recovers what the filesystem and its journal still remember, including
older copies of overwritten files that survive in journal transactions.
Everything restored is written into `./RESTORED_FILES/`. It handles
ext3 only (not ext4), and by the author's own description it does not
work 100% automatically — expect to iterate with `--inode`, `--ls`,
and `--dump-names` to find candidates before restoring.

## Typical use cases

### List deleted entries in the root directory

First look at what was deleted.

```
ext3grep /dev/sdb1 --ls --deleted
```

Outcome: a directory listing of the filesystem root filtered to
deleted entries (inode, type, name) — the candidate list for recovery;
swap `/dev/sdb1` for an image file as needed.

### Enumerate file paths the tool can see

Get restore-ready paths in one shot.

```
ext3grep /dev/sdb1 --dump-names
```

Outcome: the path of every file found in directory entries is written
to stdout (this implies `--ls` but suppresses its listing); combine
with `--deleted` to restrict output to deleted paths.

### Restore one file by path

Recover a specific deleted file.

```
ext3grep /dev/sdb1 --restore-file documents/report.txt
```

Outcome: `report.txt` is restored to `./RESTORED_FILES/documents/report.txt`
— the path is relative to the partition root and must be one of the
paths `--dump-names` printed; success depends on the data blocks still
being intact.

### Restore everything recoverable

The broad sweep over the whole filesystem.

```
ext3grep /dev/sdb1 --restore-all
```

Outcome: every file the tool can match to recoverable data is written
under `./RESTORED_FILES/`, preserving paths — expect a mix of complete
and partially recovered files to review.

### Inspect a specific inode

Drill into one file's metadata.

```
ext3grep /dev/sdb1 --inode 1234
```

Outcome: detailed information on inode 1234 (without `--ls`, printing
its block references/content is implied); use `--inode <dir-inode>
--ls` instead to list a specific directory's entries.

### Recover an overwritten file from the journal

Use journal history for files that were replaced, not deleted.

```
ext3grep /dev/sdb1 --restore-inode 1234@8500
```

Outcome: the copy of inode 1234 stored in the journal entry with
sequence number 8500 is restored as `./RESTORED_FILES/inode.1234` —
without `@seqnr` the latest journal copy is used; `--journal` alone
shows journal content to search for sequence numbers.

*No corpus evidence — derived from upstream documentation.*
