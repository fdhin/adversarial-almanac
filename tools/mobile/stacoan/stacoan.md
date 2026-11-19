---
name: "stacoan"
categories:
  - blackarch-mobile
version: "0.90"
homepage: "https://github.com/vincentcox/StaCoAn"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "vincentcox/StaCoAn"
github-stars: 869
github-pushed: "2021-04-27"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stacoan

> Drag-and-drop static analyzer that decompiles mobile apps and greps them for secrets, producing a shareable HTML report.

## What it's for

StaCoAn (Vincent Cox) performs static code analysis on mobile applications: it decompiles an APK with its bundled JADX, then matches every source, markup and database file against editable regex wordlists to surface hardcoded credentials, API keys, decryption keys, API URLs and major coding mistakes. Findings land in a portable, responsive HTML report with a decompiled-file tree viewer, a database table viewer, and a "loot" feature that lets you bookmark interesting lines and export them. Wordlists (`regex|||confidence|||description` per line) and an exclusion list are user-editable, which is the main way to tune results.

Only APK input works (IPA support was on the roadmap, never finished), and obfuscated code defeats it. The project is no longer maintained, but the release binary still runs on Linux.

## Typical use cases

### Scan an APK directly from the command line

Skip the interface and generate the report for one app.

```
stacoan -p app.apk
```

Outcome: decompiles the APK, applies the wordlists to the extracted sources and databases, and writes a folder named after the APK containing the browsable HTML report — open it in a browser to walk findings through the file tree.

### Start the drag-and-drop web interface

Launch server mode to process apps through a browser instead of the CLI.

```
stacoan
```

Outcome: starts the local web server; browse to `http://127.0.0.1:7777` and drag an APK onto the page — the app is uploaded, analyzed, and its report is offered for download.

*No corpus evidence — derived from upstream documentation.*
