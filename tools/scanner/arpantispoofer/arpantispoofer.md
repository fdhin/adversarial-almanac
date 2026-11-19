---
name: "arpantispoofer"
gentoo-category: "net-analyzer"
version: "1.0.1.32"
homepage: "http://sourceforge.net/projects/arpantispoofer/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# arpantispoofer

> GTK utility that detects and resists bidirectional ARP spoofing on the local segment.

## What it's for

ARP AntiSpoofer (SourceForge project, packaged by Pentoo) watches ARP traffic on the LAN
and alerts on — and actively resists — ARP spoofing/poisoning attempts, in both
directions: it can protect the local host as well as other hosts in the same subnet, and
doubles as a helper for gateways whose ARP handling misbehaves. It is a GNOME/GTK-era
graphical application (the ebuild depends on `libgnomeui`), not a CLI: the Pentoo package
even removes the broken launcher symlink from `/usr/bin`, leaving the GUI as the only
interface, so there is no command-line surface to document and this note is
description-only. For scriptable ARP-spoof detection on modern systems, arpwatch or
arp-scan-based monitoring are the current equivalents.

*No corpus evidence — description-only note (GUI application, no CLI); identity from the Pentoo ebuild and the SourceForge project page.*
