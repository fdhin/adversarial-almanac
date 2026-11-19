---
name: "sdrangelove-git"
categories:
  - radio
version: "20180313.r175"
homepage: "http://sdr.osmocom.org/trac/wiki/sdrangelove"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: cmake
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sdrangelove-git

> SDRangelove — the osmocom-era Qt/OpenGL SDR frontend for OsmoSDR and RTL-SDR, discontinued around 2016.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SDRangelove was a Qt SDR receiver application developed by Christian Daniel and hosted on OsmoSDR git/trac (built-in support for OsmoSDR and rtl-sdr, plus FUNcube and HackRF-class hardware through gr-osmosdr), packaged here as a 2018 git snapshot by ArchStrike. The project is dead: the osmocom trac and git hosting are gone, and the package is a historical artifact rather than a maintained receiver. It is not the same codebase as the modern SDRangel by a rename — f4exb's SDRangel states it is a complete redesign of RTL-SDRangelove (a related project by another author) — but the two fill the same niche. For any current use, see [[sdrangel]]; nothing here applies to hardware or workflows that the maintained successor does not cover better.
