---
name: "neglected"
categories:
  - blackarch-recon
version: "8.68d02b3"
homepage: "https://github.com/GuerrillaWarfare/neglected"
packaged-by: [blackarch]
blackarch-builddate: "2015-08-17"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "GuerrillaWarfare/neglected"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# neglected

> Facebook CDN photo URL resolver (historical, Python 2); upstream repository is gone.

## What it's for

neglected was a small Python 2 script (packaged by BlackArch in 2015, depending on `python2-requests`) described as a "Facebook CDN Photo Resolver": given Facebook content-delivery-network photo URLs, it resolved them back toward the photos they referenced — the kind of lookup used to de-anonymize or archive images whose public links had been rotated. The BlackArch package installed a `/usr/bin/neglected` wrapper that runs the script `neglected` out of `/usr/share/neglected/` with any arguments passed through.

Nothing runnable can be documented today: the upstream repository (GuerrillaWarfare/neglected) has been deleted and is not captured by the Internet Archive, so the command-line interface is unverifiable, and Facebook's CDN URL scheme has been redesigned repeatedly since 2015 — any technique the script used is long obsolete. This note is a historical record only; no usage examples are given because none can be verified against the packaged source.

*No corpus evidence — description-only. Identity from the BlackArch PKGBUILD (pkgdesc "Facebook CDN Photo Resolver", python2 + python2-requests, /usr/bin wrapper); upstream gone, no archived copy found.*
