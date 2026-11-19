---
name: "exe2image"
categories:
  - blackarch-backdoor
version: "1.1"
homepage: "https://github.com/OsandaMalith/Exe2Image"
packaged-by: [blackarch]
blackarch-builddate: "2017-10-18"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "OsandaMalith/Exe2Image"
github-stars: 208
github-pushed: "2015-08-21"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# exe2image

> Windows utility that converts an EXE file into a PNG image and back, hiding executables inside image files.

*No corpus evidence — derived from upstream documentation. No documented CLI — description-only note.*

## What it's for

Turning a payload executable into an ordinary image file (and recovering the executable later), so it survives casual inspection and filters that trust image types — a delivery-stage trick rather than an exploit. Upstream (OsandaMalith/Exe2Image) distributes it as a Windows GUI application in a 7z archive from the releases page; the repository itself contains only the README, documents no command-line interface and exposes no switches, so no runnable examples can be given. Note the upstream README says PNG, not JPEG as earlier catalog text suggested.
