---
name: "cubicsdr"
categories:
  - blackarch-radio
version: "0.2.7"
homepage: "https://github.com/cjcliffe/CubicSDR"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-10-03"
build-system: cmake
license: GPL2
meta-checked: "2026-11-09"
github-repo: "cjcliffe/CubicSDR"
github-stars: 2302
github-pushed: "2026-11-07"
github-archived: false
github-issues: 246
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 163
gh-activity-checked: "2026-11-09"
---

# cubicsdr

> Cross-platform desktop SDR receiver application built on the SoapySDR hardware abstraction.

## What it's for

CubicSDR is a graphical software-defined radio receiver for Linux, macOS and Windows. Through SoapySDR it drives a wide range of hardware — RTL-SDR, Airspy, HackRF, USRP, SDRplay, LimeSDR and more — from a single interface, offering a panadapter spectrum and 3D waterfall, multiple simultaneous demodulators (FM, AM, SSB, plus data modes), frequency bookmarks and audio/file recording; DSP comes from liquid-dsp. Optional hamlib integration adds radio control for tracking and scanning workflows. It is a pure GUI application: launched as `cubicsdr` with no command-line options (the source registers no argument parsing beyond the wxWidgets entry macro), so device selection, gain and demodulator setup all happen interactively and there is no scriptable CLI surface to document. The manual lives at cubicsdr.readthedocs.io.

*No corpus evidence — derived from upstream documentation.*
