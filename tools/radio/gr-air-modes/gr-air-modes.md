---
name: "gr-air-modes"
categories:
  - blackarch-radio
version: "396.0b6c383"
homepage: "https://github.com/bistromath/gr-air-modes"
packaged-by: [blackarch]
blackarch-builddate: "2018-09-28"
build-system: cmake
license: GPL3
meta-checked: "2026-11-09"
github-repo: "bistromath/gr-air-modes"
github-stars: 485
github-pushed: "2021-02-12"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gr-air-modes

> GNU Radio receiver for Mode S transponders and ADS-B on 1090 MHz.

## What it's for

gr-air-modes (by Nick Foster) is a GNU Radio module implementing a Mode S / ADS-B receiver: it demodulates 1090 MHz downlink replies, checks parity, decodes the common ADS-B reports (position via Compact Position Reporting, velocity, identification) and prints aircraft data with range and bearing from your station. Beyond stdout, output can go to a SQLite database, Google Earth KML, an SBS-1-compatible TCP server for viewers such as Virtual Radar Server and PlanePlotter, or a FlightGear multiplayer feed. The main program is `modes_rx`; sample sources are USRP (UHD, the default), RTL-SDR via the Osmocom driver, or a recorded file or network stream given to `-s`. It listens to 1090 MHz downlink only (1030 MHz interrogations are not decoded). Note it is a Python 2 / older-GNU-Radio-era codebase — modern stations usually run dump1090-family receivers instead; run `modes_rx --help` for the full option list on your build.

## Typical use cases

### Receive ADS-B from a USRP with defaults

```
modes_rx
```

Outcome: opens the first UHD device at 1090 MHz, 4 MS/s, and prints every decoded Mode S reply — ICAO address, altitude, callsign and, once enough CPR messages arrive, position.

### Use an RTL-SDR dongle with a known station location

```
modes_rx -s osmocom -g 40 --location 40.00000,-100.00000
```

Outcome: receives through the Osmocom RTL-SDR source at 40 dB gain; `--location` supplies the station coordinates so range and bearing are computed for each aircraft in the printout.

### Feed Virtual Radar Server over SBS-1

```
modes_rx -s osmocom -P
```

Outcome: besides console output, opens an SBS-1-compatible server on TCP port 30003 that PlanePlotter, Virtual Radar Server and other compatible viewers connect to as a data feed.

### Log traffic to Google Earth KML

```
modes_rx -s osmocom -K traffic.kml --location 40.00000,-100.00000
```

Outcome: stores decoded reports in a local SQLite database (`adsb.db`) and continuously regenerates `traffic.kml` with aircraft positions and range rings around your station, viewable live in Google Earth.

### Publish raw reports on a TCP port

```
modes_rx -s osmocom -t 45678
```

Outcome: opens a TCP server on port 45678 publishing the raw report stream so other tools — including additional gr-air-modes instances via `--remote` — can consume the feed.

*No corpus evidence — derived from upstream documentation.*
