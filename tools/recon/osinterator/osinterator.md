---
name: "osinterator"
categories:
  - blackarch-recon
version: "3.8447f58"
homepage: "https://github.com/guitarmanj/OSINTerator"
packaged-by: [blackarch]
blackarch-builddate: "2015-11-03"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "guitarmanj/OSINTerator"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# osinterator

> An early-2010s Python 2 "Open Source Toolkit for Open Source Intelligence Gathering"; its repository is gone and no usable documentation survives.

## What it's for

OSINTerator (guitarmanj) was a small open-source intelligence toolkit that BlackArch packaged in 2015 as a Python 2 application depending on `python2-xgoogle` — a library for scraping Google search results programmatically. That dependency is the main surviving technical signal: the tool automated search-engine-driven information gathering of the pre-API era, complementing the platform-specific scanners of its time. Its GitHub repository has been deleted, the Wayback Machine holds no capture of the repo or its README, and the packaging metadata (BlackArch PKGBUILD, MIT license, the one-line description above) is the only authoritative record left. No runnable interface can be verified today, so this note documents the tool's existence and lineage rather than any usage: with Python 2 itself end-of-life and the dependency ecosystem gone, there is no supported way to execute it on a current system. Anyone needing this capability today should use a maintained OSINT framework; the vault's recon category carries several.

> No corpus evidence — upstream repository deleted; description reconstructed from BlackArch packaging metadata only.
