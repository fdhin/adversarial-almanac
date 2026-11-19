---
name: "autoscan-network"
gentoo-category: "net-analyzer"
version: "1.50"
homepage: "http://autoscan-network.com/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: make
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# autoscan-network

> Autoscan-Network — a GTK network exploration and monitoring utility that discovers hosts on the local network in real time, watches for new machines, and integrates Samba share browsing and mounting.

*No corpus evidence — derived from upstream documentation. Description-only: a GUI application from a defunct upstream, with no documented command-line interface.*

## What it's for

Continuous network inventory from the desktop. Unlike one-shot CLI scanners, Autoscan monitors connected networks and surfaces hosts as they appear or disappear, builds an inventory with per-host details, and can browse and mount Samba-shared folders directly from its interface — the "with Samba support" in its description. It dates from the GTK era of the late 2000s (v1.50, GPL-2, packaged by Pentoo); the original project site (autoscan-network.com / autoscan.free.fr) is defunct and no source repository survives, so treat it as legacy software for old-network archaeology rather than a modern assessment tool. Reach for it when you want a passive-style LAN monitor with share browsing; for scripted discovery use nmap or a maintained alternative instead.
