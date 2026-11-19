---
name: "gps-sdr-sim"
categories:
  - blackarch-radio
version: "224.28ca29a"
homepage: "https://github.com/osqzss/gps-sdr-sim"
packaged-by: [blackarch]
blackarch-builddate: "2025-01-18"
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "osqzss/gps-sdr-sim"
github-stars: 3474
github-pushed: "2025-01-26"
github-archived: true
github-issues: 173
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gps-sdr-sim

> Software-defined GPS L1 baseband signal generator for GNSS receiver testing.

## What it's for

gps-sdr-sim (by Takuji Ebinuma) generates digitized GPS L1 C/A baseband I/Q files from a RINEX navigation (ephemeris) file plus a chosen receiver trajectory — a static position on the command line, an ECEF or lat/lon/height CSV motion file at 10 Hz, or an NMEA GGA stream. It computes simulated pseudorange and Doppler for the satellites in view and renders the resulting signal samples, which are then replayed towards a GPS receiver under test through an SDR transmitter (HackRF, bladeRF, USRP, ADALM-Pluto) behind a DC block and 50–60 dB attenuation — the standard way to bench-test GPS acquisition and tracking without live satellites. It simulates GPS L1 only (no GLONASS/Galileo/BeiDou) and produces the file; RF transmission is done by the SDR platform's own tools.

## Typical use cases

### Simulate a static location

```
gps-sdr-sim -e brdc3540.14n -l 30.286502,120.032669,100
```

Outcome: writes `gpssim.bin` (default 2.6 MS/s, 16-bit I/Q) holding the GPS signal a stationary receiver at the given latitude, longitude and height would see under the ephemerides in the RINEX file.

### Simulate a moving receiver from a CSV track

```
gps-sdr-sim -e brdc3540.14n -u circle.csv -d 300
```

Outcome: generates 300 seconds of signal following the ECEF x,y,z positions in `circle.csv` (10 Hz samples) — used to exercise receiver tracking loops under dynamics.

### Drive the scenario from a recorded NMEA stream

```
gps-sdr-sim -e brdc3540.14n -g track.nmea
```

Outcome: reads GGA sentences as the receiver track and synthesizes the matching signal file, letting a logged field trajectory be replayed at the bench.

### Produce an 8-bit file for HackRF replay

```
gps-sdr-sim -e brdc0010.22n -b 8
hackrf_transfer -t gpssim.bin -f 1575420000 -s 2600000 -a 1 -x 0
```

Outcome: the first command writes signed 8-bit I/Q samples (`-b 8`, a quarter of the 16-bit file size, the format hackrf_transfer expects); the second replays them at the GPS L1 frequency of 1575.42 MHz towards the receiver under test.

### Pin the scenario time and sample rate

```
gps-sdr-sim -e brdc3540.14n -l 30.286502,120.032669,100 -t 2024/06/01,12:00:00 -s 2500000 -o scenario.bin
```

Outcome: generates the scenario starting at the given date and time (ephemerides must cover it) at 2.5 MS/s into `scenario.bin` — the rate USRP hardware requires since its decimator needs an integer divide of 100 MHz.

*No corpus evidence — derived from upstream documentation.*
