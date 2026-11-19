---
name: "deskhpsdr"
categories:
  - blackarch-radio
version: "2.6.65.r6.gdb2316c"
homepage: "https://github.com/dl1bz/deskhpsdr"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-13"
build-system: cmake
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "dl1bz/deskhpsdr"
github-stars: 101
github-pushed: "2026-11-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1067
gh-activity-checked: "2026-11-09"
---

# deskhpsdr

> Desktop transceiver front-end for OpenHPSDR-protocol SDR radios (Hermes Lite 2, ANAN and compatible).

## What it's for

deskHPSDR (by DL1BZ) is a GTK3 desktop application for hamradio SDR transceivers speaking the OpenHPSDR protocol version 1 or 2 over Ethernet — Hermes Lite 2, ANAN and similar platforms — supporting full RX/TX operation for everyday use. It descends from pihpsdr but is rebuilt for desktop systems, and builds on the WDSP DSP library for PureSignal pre-distortion, noise reduction (NR1–NR4, NNR) and the complete RX/TX audio chain. Interoperability with the rest of a ham station is via TCI 2.0 (CAT, audio, I/Q), an included rigctld, CAT-over-TCP and MIDI control; verified to pair with WSJT-X family digimode apps. It deliberately excludes SoapySDR and RTL-based hardware, supports at most two receivers and one transmitter, and requires a 1280×600 or larger screen. Operation is through the GUI; the only command-line option is a version query.

## Typical use cases

### Check the build and WDSP version

```
deskhpsdr -V
```

Outcome: prints the application version, build branch and commit, build date, compiler, compile-time options and audio module to stderr, then exits — the quick way to confirm which feature set (and for SATURN devices, compatible FPGA range) a given binary carries before wiring it to a radio.

*No corpus evidence — derived from upstream documentation.*
