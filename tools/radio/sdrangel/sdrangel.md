---
name: "sdrangel"
categories:
  - blackarch-radio
version: "v8.0.0.r0.g0282a59"
homepage: "https://github.com/f4exb/sdrangel"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: cmake
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "f4exb/sdrangel"
github-stars: 4155
github-pushed: "2026-11-05"
github-archived: false
github-issues: 97
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1768
gh-activity-checked: "2026-11-09"
---

# sdrangel

> Qt/OpenGL SDR and signal analyzer with a GUI flavor and a headless web-API server flavor.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SDRangel is an open-source SDR front end and signal analyzer covering a wide range of hardware (RTL-SDR, Airspy, HackRF, PlutoSDR, LimeSDR, SDRplay and more, including via SoapySDR) with a plugin architecture: many receive and transmit channels (analog voice, digital modes, ADS-B, AIS, radio astronomy, channel analyzers) can run in parallel on one or more devices. It ships as two binaries — the Qt GUI and `sdrangelsrv`, a server flavor without GUI/OpenGL dependencies that is driven entirely through its web REST API, typically paired with the SDRangelcli web client or Docker images. Read the wiki's Quick Start before first run; device support and plugins vary by build options.

## Typical use cases

### Start the desktop application

```
sdrangel
```

Outcome: the Qt window opens with a device set; add a device (for example an RTL-SDR), then stack channel plugins on it — each channel gets its own GUI, and the spectrum/waterfall views render live.

### List SDR devices on a headless host

```
sdrangelsrv --list-devices
```

Outcome: the server binary starts without a GUI, enumerates every SDR device it can open, prints them and exits — the quick check that drivers and USB permissions are right before deploying an unattended receiver.

### Run headless under the web API

```
sdrangelsrv -a 198.51.100.10 --start
```

Outcome: the REST API binds to the given address (`-p` changes the port) and `--start` brings all configured devices and features up at launch; devices and channels are then added or tuned over HTTP from SDRangelcli or scripts, with no display on the host.
