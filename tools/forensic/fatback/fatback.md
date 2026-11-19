---
name: "fatback"
categories:
  - forensic
version: "1.3"
homepage: "http://fatback.sourceforge.net/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "gaul/fatback"
github-stars: 1
github-pushed: "2013-02-04"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fatback

> Recovers deleted files from FAT12/FAT16/FAT32 file systems, in automated or interactive shell-like mode.

## What it's for

fatback undeletes files from FAT file systems (originated at the DoD Computer Forensics Lab). Deleted FAT entries keep everything but the first character of their name, so recovery is mostly a matter of following the still-intact cluster chain; fatback automates that and adds an interactive interpreter modeled on a UNIX shell (`ls`, `cd`, `cp`, `stat`, ...) for selective recovery, including exploring lost cluster chains to reconstruct deleted directories. It reads a raw image or device, handles partitioned drives, and writes an audit log of every operation by default. Automated mode only processes a single partition; for multi-partition images use the interactive partition menu or `-p`. It targets FAT only — for ext or NTFS volumes use a file-system-specific recovery tool.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Recover all deleted files from a FAT partition image

```
fatback -a -o /mnt/recovery fat.img
```

Outcome: non-interactively recovers every deleted file the tool can find in the image into `/mnt/recovery` (recreating the source subdirectory layout), and writes an audit log to `fatback.log` in the current directory.

### Recover from one partition of a partitioned disk image

```
fatback -a -p 2 -o /mnt/recovery disk.img
```

Outcome: goes directly to partition 2 of the image and recovers its deleted files to `/mnt/recovery`, skipping the interactive partition menu.

### Selective recovery in the interactive interpreter

```
fatback disk.img
```

Outcome: opens the partition menu (if the image is partitioned) and then a `fatback>` prompt where `ls`, `cd` and `stat` explore deleted entries and `cp FILE /mnt/recovery` copies a specific deleted file out. Names are case-sensitive: use the uppercase short name (`PROGRA~1`) or the quoted long file name (`"Program Files"`).

### Hunt for orphaned directory content

```
lostchains
```

Outcome: inside the interpreter, lists lost cluster chains in the current partition — starting points for recovering deleted directories whose FAT entries were zeroed; `cpchain START_CLUSTER /mnt/recovery/chain.bin` then dumps a chosen chain to a file.

### Keep a full audit trail for the case record

```
fatback -a -l /mnt/case/fatback-audit.log --level=10 -o /mnt/recovery fat.img
```

Outcome: same automated recovery, but the audit log goes to the named file with maximum verbosity (`--level` 1–10) — the log records the command line, environment, partition info and each recovered file, as needed for forensic documentation.
