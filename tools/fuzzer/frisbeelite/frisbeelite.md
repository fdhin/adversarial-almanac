---
name: "frisbeelite"
categories:
  - blackarch-fuzzer
version: "1.2"
homepage: "https://github.com/nccgroup/FrisbeeLite"
packaged-by: [blackarch]
blackarch-builddate: "2025-06-08"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nccgroup/FrisbeeLite"
github-stars: 65
github-pushed: "2017-10-12"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# frisbeelite

> NCC Group's Frisbee Lite — a GUI-based USB device fuzzer (wxPython + pyUSB) for interactively probing and mutating USB requests against real hardware.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Frisbee Lite (v1.2, by Andy Davis of NCC Group, released under the GNU AGPLv3) fuzzes USB devices from the host side through libusb/pyUSB: the operator selects the target device and the descriptor/request parameters in a wxWidgets GUI and replays mutated USB requests against the physical device, watching how it reacts. It is a Python 2 application with no command-line interface — everything happens in the GUI, which is why this note documents no example commands. The upstream repository ships the "Fuzzing USB devices with Frisbee Lite" whitepaper PDF alongside the dependency bundles it was developed against (libusb-win32 1.2.5.0, pyusb 1.0.0-a1); the standard pre-requisites are Python, wx, libusb and pyusb.
