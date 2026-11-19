---
name: "autopsy"
categories:
  - blackarch-forensic
version: "4.23.1"
homepage: "https://github.com/sleuthkit/autopsy"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-06-29"
build-system: scripts
license: MIT, Apache-2.0
meta-checked: "2026-11-09"
github-repo: "sleuthkit/autopsy"
github-stars: 3430
github-pushed: "2026-06-20"
github-archived: false
github-issues: 406
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 136
gh-activity-checked: "2026-11-09"
---

# autopsy

> Java-based digital forensics platform and GUI built on The Sleuth Kit.

## What it's for

Autopsy is a graphical interface to The Sleuth Kit (see the
[[sleuthkit]] note for the underlying command-line tools) and other open
source forensic utilities. Where The Sleuth Kit gives you individual CLI
tools (`fls`, `icat`, `mmls`, …), Autopsy wraps them into a case-based
workflow: add a disk image or folder of files as a data source, run ingest
modules over it (file type identification, keyword search, hash-set
matching, EXIF, email extraction, extension mismatch), then explore,
bookmark/tag, chart, and report findings from the UI. It supports E01 and
raw images via libewf/pytsk3, multi-user collaborative cases, and is
cross-platform (fully tested on Windows). It is a heavyweight Java/NetBeans
application, not a scripting tool — for batch triage of many images, the
Sleuth Kit CLI or a pipeline tool usually fits better.

## Typical use cases

### Start a case from a disk image

Launch the Autopsy GUI to open or create a case and ingest an image.

```
autopsy
```

Outcome: opens the Autopsy window (a Java runtime plus the Sleuth Kit must
be installed); from there "New Case" → add data source (raw, E01, or a
folder of files) → select ingest modules → the case index builds and
results appear in the tree view, ready for keyword search, tagging, and
"Generate Report".

*No corpus evidence — derived from upstream documentation.*
