---
name: "ipba2"
categories:
  - blackarch-forensic
version: "95.c03bd85"
homepage: "http://www.ipbackupanalyzer.com/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "PicciMario/iPhone-Backup-Analyzer-2"
github-stars: 109
github-pushed: "2014-05-05"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ipba2

> GUI browser that decodes an iTunes iPhone/iPad backup directory into the original filesystem tree, with plist, SQLite, hex and image viewers.

## What it's for

iPhone Backup Analyzer 2 (iPBA2) by Mario Piccinini (2013), the successor of the original iPhone Backup Analyzer. It ingests the hash-named files of an iTunes backup, builds the decoded filesystem in an in-memory SQLite database, and presents the tree with real file names and paths. Selecting a file shows its properties (backup and real name, permissions, owner/group, iOS data hash, timestamps, type from magic numbers); right-clicking opens one of the built-in viewers — ASCII text, plist structure (plain and binary), SQLite database, hex, image. A plugin system ships artifact views for call history, SMS, WhatsApp, address book, Safari history/bookmarks, notes, known networks, Skype and thumbnails. It is a Python 2/Qt4-era GUI application with no command-line interface, and it analyzes backups on disk only — for live device acquisition see [[iosforensic]].

## Typical use cases

### Open a backup and explore it

Point the analyzer at an iTunes backup directory and browse the reconstructed filesystem.

```
ipba2
```

Outcome: the GUI starts (packaged wrapper for `python main.py`); open the backup directory through the directory picker and the decoded filesystem tree appears, ready for the viewers and plugin views. In a source checkout the equivalent is `make build` once, then `make run`.

*No corpus evidence — derived from upstream documentation.*
