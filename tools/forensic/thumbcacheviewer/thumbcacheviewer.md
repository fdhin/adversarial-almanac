---
name: "thumbcacheviewer"
categories:
  - blackarch-forensic
version: "1.0.3.7"
homepage: "https://github.com/thumbcacheviewer/thumbcacheviewer"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-19"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "thumbcacheviewer/thumbcacheviewer"
github-stars: 171
github-pushed: "2023-10-25"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# thumbcacheviewer

> Windows tool that parses thumbcache/iconcache databases and exports cached thumbnails and CSV metadata.

## What it's for

Thumbcache Viewer (Eric Kutcher) reads the Windows thumbnail cache databases — `thumbcache_*.db`, `iconcache_*.db` and their SQLite index files — and displays or exports every cached thumbnail together with its cache-entry metadata. Because Explorer caches thumbnails of media the user browsed, these databases often still contain images of files that were later renamed or deleted, making them a staple of Windows user-activity forensics. It is primarily a GUI Windows application, but it accepts database paths plus export options on the command line, including fully headless export. It parses cache databases, not arbitrary images, and can map cache-entry hashes back to source file paths via the Windows Search index when available.

## Typical use cases

### Open a thumbcache database for interactive review

Browse what Explorer cached on a user profile.

```
thumbcacheviewer.exe "C:\case\profile\thumbcache_256.db"
```

Outcome: loads the database and opens the GUI listing every cache entry with its thumbnail, hash and offsets — no installation on the evidence source needed, just the copied database files.

### Headless export of cached thumbnails

Script the image extraction.

```
thumbcacheviewer.exe "C:\case\thumbcache_256.db" -o C:\case\thumbs -z
```

Outcome: saves every cached thumbnail image into the directory `C:\case\thumbs` and exits without showing any window or prompts; `-z` skips blank entries to keep the output clean. Multiple database paths may be passed in one invocation.

### Headless CSV export of cache entries

Get the entry metadata in tabular form.

```
thumbcacheviewer.exe "C:\case\thumbcache_256.db" -c C:\case\csv
```

Outcome: writes CSV files describing the cache entries (per-entry metadata) into `C:\case\csv`, again with no GUI — suitable for batch-processing a set of databases from other tooling.

*No corpus evidence — derived from upstream documentation (the repository ships no README; flags verified against `WinMain` argument handling and the export routines in `thumbcache_viewer.cpp`).*
