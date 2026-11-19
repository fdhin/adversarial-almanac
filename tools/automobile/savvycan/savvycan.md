---
name: "savvycan"
categories:
  - blackarch-automobile
version: "continuous.r0.g7f8f605"
homepage: "https://github.com/collin80/SavvyCAN"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "collin80/SavvyCAN"
github-stars: 1919
github-pushed: "2026-11-04"
github-archived: false
github-issues: 279
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 386
gh-activity-checked: "2026-11-09"
---

# savvycan

> Qt-based cross-platform CAN bus tool — capture, visualize, reverse engineer and replay CAN frames in a GUI, working either live against an adapter or fully offline on log files.

## What it's for

A Qt5 (Qt6 port in progress on the QT6WIP branch) GUI for loading, saving, capturing and decoding CAN bus traffic, aimed at visualization, reverse engineering, debugging and capture. Live capture works through any Qt SerialBus driver — socketcan on Linux, plus Vector, PeakCAN and TinyCAN devices — and pairs naturally with the GVRET firmware on EVTV's CANDue board, though no capture device is required: it reads and writes a long list of formats (BusMaster, Microchip, CRTD/OVMS, GVRET native, generic CSV, Vector Trace, IXXAT Minilog, CAN-DO, Vehicle Spy, and read-only candump/Kayak, PCAN Viewer and Wireshark socketcan PCAP), so existing captures can be inspected and annotated without hardware. Typical GUI workflows include frame filtering, graphing signals, DBC decoding, sending/replaying frames and fuzzing — all interactively, in contrast to the CLI-filter style of can-utils. It is a desktop application with no scriptable interface; everything is menu- and dialog-driven and needs a 1024x768 or larger display.

## Typical use cases

### Launch the CAN bus workbench

Open the GUI against local adapters or log files.

```
./SavvyCAN
```

Outcome: the main window opens; adding a connection (Connections dialog, e.g. socketcan `can0`) starts live capture with frames streaming into the main view, while File > Load pulls in an existing log (BusMaster, GVRET, candump/Kayak, PCAP and more) for offline analysis — both can then be filtered, graphed, DBC-decoded and re-exported in any of the supported formats.

*No corpus evidence — derived from upstream documentation.*
