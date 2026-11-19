---
name: "mtscan"
categories:
  - blackarch-wireless
version: "176.dd44f0e"
homepage: "https://github.com/kkonradpl/mtscan"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-19"
build-system: cmake
license: GPL2
meta-checked: "2026-11-09"
github-repo: "kkonradpl/mtscan"
github-stars: 38
github-pushed: "2026-07-09"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# mtscan

> GTK+ scanner that surveys wireless networks through MikroTik RouterOS devices, logging APs, clients, signal, GPS, and azimuth data.

*No corpus evidence — derived from upstream documentation.*

## What it's for

mtscan (by Konrad Kosmatka) turns MikroTik routers running RouterOS into remote wireless sensors: it connects to a router over SSH, drives its wireless scan, and collects the networks and clients that router observes — with signal samples, GPS position, and azimuth — into logs you can review and filter in the GUI; TZSP streams are supported as an alternative data feed. Because the radios doing the observing are the routers themselves, your survey reach extends to wherever you have a RouterOS device, and scans can be logged headlessly for wardriving-style collection. It requires GTK+ 2, libssh, yajl, zlib, openssl, and libcurl at build time.

## Typical use cases

### Run the scanner GUI

Open the interactive survey window.

```
mtscan
```

Outcome: starts the GTK+ interface; configure RouterOS connection profiles, launch scans, and watch detected networks and clients live with their signal data.

### Headless batch scan to a log file

Collect scan results without the GUI, for example from a script or a machine attached to a router.

```
mtscan -b -o scan.log
```

Outcome: batch mode (`-b`, which requires `-o`) performs the scan and writes results to scan.log without opening a window; `-d <dir>` overrides the autosave directory, `-t <port>` overrides the TZSP UDP port.

### Auto-connect to a saved profile

Start scanning straight into a stored RouterOS connection.

```
mtscan -a 1
```

Outcome: connects automatically to connection profile id 1 and begins the scan without the manual connect dialog; `-s` additionally skips SSH host-key verification.
