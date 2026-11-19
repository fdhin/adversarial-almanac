---
name: "wifiscanmap"
categories:
  - blackarch-wireless
version: "135.9adcd08"
homepage: "https://github.com/mehdilauters/wifiScanMap"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "mehdilauters/wifiScanMap"
github-stars: 155
github-pushed: "2017-07-27"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wifiscanmap

> Wardriving mapper: geotags Wi-Fi access points, client probes and Bluetooth devices into SQLite and charts them on a web map.

## What it's for

wifiScanMap records the radio neighborhood while you move: it polls iwlist (or airmon-ng in monitor mode) for 802.11 access points and stations, hcitool for Bluetooth devices, and a gpsd-connected GPS receiver for position, logging everything with timestamps into an SQLite database. A bundled web HMI (Angular/OpenLayers) maps the results — APs with BSSID/ESSID/encryption/manufacturer, per-station location histories, and probe-request counts showing which SSIDs nearby clients are looking for — and can also estimate your own position from already-known APs. Developed 2016-2017 against Raspberry Pi/Debian-era tooling and unmaintained since (last upstream push 2017); it is a wardriving/measurement tool, not an attack tool — it does not inject or crack anything.

## Typical use cases

### Monitor-mode wardrive with GPS logging

Capture access points, stations and probe requests while recording position.

```
sudo python scanmap.py -m
```

Outcome: switches to airmon-ng-based capture, so besides AP metadata every observed station and probe request is recorded with signal, date and GPS position into the SQLite database; the web HMI serves on http://localhost:8686 — open it to watch the map fill as data is collected (run `iwconfig`-level prerequisites: gpsd running, adapter capable of monitor mode).

### Feed a remote collector

Synchronize captures from a mobile node to a central instance.

```
sudo python scanmap.py -m -e 198.51.100.10
```

Outcome: in addition to local logging, recorded data is synchronized to the remote wifiScanMap instance at the given address (running the same program in server mode), so several scanning nodes — e.g. a drone or bike-mounted Pi — accumulate into one shared map.

*No corpus evidence — derived from upstream documentation (invocation and `-m`/`-e` behavior verified against the mehdilauters/wifiScanMap README).*
