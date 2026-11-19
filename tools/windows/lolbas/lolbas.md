---
name: "lolbas"
categories:
  - blackarch-windows
version: "192.d148d27"
homepage: "https://github.com/api0cradle/LOLBAS"
packaged-by: [blackarch]
blackarch-builddate: "2018-12-16"
build-system: windows
meta-checked: "2026-11-09"
github-repo: "api0cradle/LOLBAS"
github-stars: 1614
github-pushed: "2018-12-10"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lolbas

> The LOLBAS dataset — a searchable catalog of living-off-the-land Windows binaries, libraries and scripts and their abuse functions.

## What it's for

LOLBAS (Living Off the Land Binaries And Scripts) is a reference dataset, not a program: a curated catalog of signed/native Windows binaries, libraries and scripts (LOLBins, LOLLibs, LOLScripts) that can be abused to download, encode, execute, persist or bypass — each entry documents a file's abuse functions with copy-pasteable example command lines. This package ships the original api0cradle repository snapshot (frozen 2018, kept for historical reasons) at `/usr/share/windows/lolbas`, one markdown file per entry grouped into OSBinaries, OSLibraries, OSScripts, OtherBinaries, OtherMSBinaries and OtherScripts. The live project has since moved to the LOLBAS-Project organization — the current, larger dataset (yml entries) and its search portal live at https://lolbas-project.github.io/ and https://github.com/LOLBAS-Project/LOLBAS — so treat the packaged tree as an offline historical snapshot and the portal as the up-to-date reference.

## Typical use cases

### Look up the abuse functions of a known binary

```
cat /usr/share/windows/lolbas/OSBinaries/Certutil.md
```

Outcome: the entry for that binary — its listed Functions (e.g. Download, Encode, Decode, ADS), working example command lines per function, acknowledgements and resources; use it to pick a dual-use native binary for a task instead of dropping a custom tool.

### Find every cataloged binary usable for a function

```
grep -il 'Functions:.*Download' /usr/share/windows/lolbas/OSBinaries/*.md
```

Outcome: filenames (one per binary) whose entry lists Download among its functions — an offline "what can fetch a file here" shortlist; repeat with other function keywords (Encode, Execute, ADS, Copy) across the OSBinaries, OSLibraries and OSScripts folders.

### Survey the packaged dataset

```
ls /usr/share/windows/lolbas/
```

Outcome: the category folders of the snapshot (OSBinaries, OSLibraries, OSScripts, OtherBinaries, OtherMSBinaries, OtherScripts) plus the top-level index files LOLBins.md, LOLLibs.md and LOLScripts.md — the indexes list every entry per category and are the fastest way to browse what the catalog covers.

*Dataset note grounded at the packaged install path (/usr/share/windows/lolbas, per the BlackArch package); no corpus evidence — derived from the upstream repository snapshot.*
