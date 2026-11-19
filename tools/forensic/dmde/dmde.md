---
name: "dmde"
categories:
  - blackarch-forensic
version: "4.2.2.816"
homepage: "https://dmde.com/download.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-11-16"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# dmde

> DM Disk Editor and Data Recovery Software: interactive partition search, file recovery, and disk editing for damaged file systems.

## What it's for

DMDE (by Dmitry Sidorov, dmde.com) is a proprietary, freemium disk editor and data-recovery application for Windows, Linux (GUI and console editions), macOS, and DOS. Its strengths are partition search and reconstruction after damage, full-surface scans that assemble file systems from remnants, RAID support, an NTFS toolset, and a raw disk editor — used both to recover lost files and to triage failing drives. It opens physical devices, sector-wise images, and VHD/VHDX/VMDK files (Pro editions also read E01). It has no scriptable command-line interface: you run `dmde` (with root or Administrator rights for device access) and work interactively from its disk-selection, file-panel, and editor screens. The Free edition recovers up to 4000 files per operation from the current panel; Express and Professional licenses remove that limit and add reporting, E01 read support, and service-use rights.

*No corpus evidence — derived from upstream documentation.*
