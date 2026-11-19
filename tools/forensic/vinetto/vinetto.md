---
name: "vinetto"
categories:
  - blackarch-forensic
version: "0.07beta"
homepage: "https://vinetto.sourceforge.net"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "AtesComp/Vinetto"
github-stars: 23
github-pushed: "2026-10-06"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---
# vinetto

> Parses Windows Thumbs.db thumbnail caches and recovers thumbnails and metadata for images that may no longer exist on disk.

## What it's for

Windows 98/ME/2000/XP and Server 2003 store a Thumbs.db file — an undocumented OLE compound file — in each folder, holding thumbnails plus metadata (original file name, dates) for the images viewed there. Vinetto, written by Michel Roukine (2007, GPL), parses these caches offline: it prints the metadata list and extracts the thumbnail images, including entries whose source picture has since been deleted, which makes Thumbs.db a standard artifact for evidence of images that were on a system. The packaged 0.07 beta is the original Python 2 script (needs PIL) and reads only Thumbs.db; the GitHub repository tracked in the frontmatter is a maintained Python 3 port of the same tool that also handles Vista+ `thumbcache_*.db` files and cross-references them against a Windows.edb ESE database. Vinetto reads thumbnail caches only — it is not a general picture carver.

## Typical use cases

### Dump the metadata table of a Thumbs.db

One sentence of intent: see which images a folder contained and when, without extracting anything.

```
vinetto Thumbs.db
```

Outcome: prints one numbered metadata line per cached thumbnail (index, original file name, dates) on standard output; no files are written.

### Extract the thumbnail images

Recover the cached previews as image files for review.

```
vinetto -o thumbnails/ Thumbs.db
```

Outcome: writes the embedded thumbnails as numbered image files under `thumbnails/` (plus a `thumbnails/.thumbs` index); add `-s` to also symlink each thumbnail's real file name to its numbered name.

### Produce an HTML report for review

Package thumbnails and metadata into a browsable report.

```
vinetto -H -U -o report/ Thumbs.db
```

Outcome: writes the thumbnails plus an HTML report of the metadata into `report/` (`-U` selects UTF-8 encoded output), ready to hand to a reviewer or open in a browser.

*No corpus evidence — derived from upstream documentation.*
