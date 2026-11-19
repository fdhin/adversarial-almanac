---
name: "mosref"
gentoo-category: "net-analyzer"
version: "2.0_beta3"
homepage: "http://sourceforge.net/projects/mosref/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: LGPL-2
meta-checked: "2026-11-09"
github-repo: "swdunlop/WaspVM"
github-stars: 107
github-pushed: "2026-10-09"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-09"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# mosref

> MOSREF ("Mosquito" Secure Remote Execution Framework): a 2000s-era Scheme-VM framework for building and deploying host and network auditing tools.

## What it's for

MOSREF is a secure remote execution framework distributed through SourceForge (project "Mosquito SREF") that provides a compact, Scheme-influenced virtual machine for the development, deployment and management of network and host security auditing tools. The 2.0 beta 3 release shipped prebuilt components for several platforms of its era (Linux x86, Windows, Darwin/PPC, OpenBSD/PPC) plus standalone VM stub images, and installs itself through an `install.ms` script written in its own Scheme dialect. It is LGPL-2 licensed and was carried by the Gentoo/Pentoo overlays (this note's packaged origin). This is a description-only entry: the SourceForge release tarballs are no longer served and no reachable documentation describes MOSREF's command interface, so no usage examples are recorded. The designer (Scott W. Dunlop) later ported MOSREF into his own WaspVM repository — `mod/bin/mosref.ms`, built by the Makefile's `mosref` target — which is the live source home linked in this note's frontmatter; its stats describe WaspVM, the successor project.

No corpus evidence — derived from upstream documentation (SourceForge project metadata and the Pentoo ebuild).
