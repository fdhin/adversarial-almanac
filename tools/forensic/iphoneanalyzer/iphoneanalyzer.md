---
name: "iphoneanalyzer"
categories:
  - blackarch-forensic
version: "2.1.0"
homepage: "http://downloads.sourceforge.net/project/iphoneanalyzer/"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "foreni-packages/iphoneanalyzer"
github-stars: 6
github-pushed: "2015-10-03"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# iphoneanalyzer

> Java Swing explorer for the iPhone filesystem: browse iTunes backups (or a jailbroken device over SSH) with plist, SQLite and hex viewers, search, reports and file export.

## What it's for

iPhone Analyzer, a GPLv3 Java GUI by Leo Crawford and Mat Proud (Cryptic Bit), hosted on SourceForge with its last release in 2012. It reconstructs the phone's filesystem from an iTunes backup on disk — or, in beta quality, over SSH from a jailbroken device — and lets an examiner walk it: native viewing of plists, SQLite databases and hex dumps, regex search across files, address book/SMS/photos, file export and recovery, reports, and geolocation of track data. Fully supports iOS 5 and earlier; iOS 6 only works partially. The project is long unmaintained, so it is a tool for legacy case data rather than current devices. For a backup-only analyzer of the same era see [[ipba2]]; for live SSH acquisition see [[iosforensic]].

## Typical use cases

### Launch the explorer

Start the GUI against a local Java runtime.

```
iphoneanalyzer
```

Outcome: the Swing application opens (`java -jar iphoneanalyzer.jar` under the packaged wrapper; from the upstream release the equivalent is `java -jar iphoneanalyzer.fat.gui-2.1.0.jar`). Open an iTunes backup directory in the application to browse its reconstructed filesystem with the viewers, search and export functions.

*No corpus evidence — derived from upstream documentation.*
