---
name: "thcsmartbrute"
categories:
  - cracker
version: "1.0"
homepage: "https://github.com/vanhauser-thc/THC-Archive/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "vanhauser-thc/THC-Archive"
github-stars: 776
github-pushed: "2025-09-03"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# thcsmartbrute

> ArchStrike package name for THC Smartbrute — the smartcard APDU discovery tool.

Same tool as [[thc-smartbrute]]: THC Smartbrute v1.0 by van Hauser / THC, which brute-forces smartcard CLA/INS command space and their P1/P2/P3 parameters over a PC/SC reader. This duplicate note exists only because the ArchStrike repo packages the identical upstream release (`thcsmartbrute-1.0.tar.gz`, mirrored in the THC-Archive GitHub repository) under this hyphen-less name, while BlackArch ships the same tarball as `thc-smartbrute` and installs the binary as `/usr/bin/thc-smartbrute`.

All usage patterns, flags and examples live in the [[thc-smartbrute]] note and are deliberately not duplicated here.

*No corpus evidence — derived from upstream documentation.*
