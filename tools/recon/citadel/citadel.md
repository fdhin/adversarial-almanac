---
name: "citadel"
categories:
  - blackarch-recon
version: "95.3b1adbc"
homepage: "https://github.com/jakecreps/Citadel"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-11-19"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "jakecreps/Citadel"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# citadel

> A categorized Python library of OSINT tools and resources, surviving only in its distro package.

## What it's for

Citadel (by Jake Creps) was a small Python "library of OSINT tools" organizing resources and lookups under five categories: Recon Tools, People Search Tools, Social Media Tools, Paste site Tools and Dark Web Tools. The GitHub repository was deleted around late 2020, so the packaged copy — installed under /usr/share/citadel with a `citadel` launcher — is the surviving artifact. The last archived repository snapshot (December 2019) confirms the layout: a `citadel.py` entry point, a requirements.txt and per-category directories. Expect unmaintained 2019-era code and external links that may no longer resolve.

## Typical use cases

### Launch the library

Open the OSINT library from the packaged launcher.

```
citadel
```

Outcome: the launcher changes into /usr/share/citadel and runs the citadel.py entry point with any given arguments; the library's resources are organized under its Recon, People Search, Social Media, Paste site and Dark Web categories for browsing from there.

*No corpus evidence — derived from the archived upstream repository and the packaging recipe.*
