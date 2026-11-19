---
name: "kalibrate-rtl"
categories:
  - blackarch-mobile
version: "69.340003e"
homepage: "https://github.com/steve-m/kalibrate-rtl"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2022-02-06"
build-system: autotools
license: custom
meta-checked: "2026-11-09"
github-repo: "steve-m/kalibrate-rtl"
github-stars: 542
github-pushed: "2023-08-15"
github-archived: false
github-issues: 22
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kalibrate-rtl

> RTL-SDR port of `kal`: measures a dongle's clock error in ppm by using GSM base stations as a reference.

## What it's for

kalibrate-rtl (binary `kal`) is Steve Markgraf's fork of Joshua Lackey's kalibrate, modified for RTL2832U dongles. It solves a specific SDR problem: cheap dongles have oscillators tens of ppm off frequency, and most SDR software expects you to supply a correction value. GSM base stations must be accurate to about 0.05 ppm, so `kal` detects their frequency-correction bursts and turns them into a reference clock: scan a GSM band to find base stations, then measure your dongle's frequency offset against one. It only corrects small offsets — for a dongle with a large unknown error, get a rough initial estimate from a wideband scanner first and feed it back with `-e`. Multiple dongles are selected with `-d`.

## Typical use cases

### Scan a GSM band for nearby base stations

Find which channels carry a detectable frequency-correction burst before measuring against one.

```
kal -s GSM900
```

Outcome: prints one line per detected channel — ARFCN, centre frequency, measured offset in Hz and relative power, e.g. `chan: 145 (872.6MHz - 7Hz)  power: 33605.48`. Supported band indicators are GSM850, GSM-R, GSM900, EGSM, DCS and PCS. Pick the strongest channel for the offset calculation.

### Measure the dongle's clock offset against a base station

Turn one known base station into the reference for a ppm measurement.

```
kal -c 145 -g 40
```

Outcome: tunes to channel 145 at 40 dB gain and prints `average [min, max] (range, stddev)` in Hz over a run of bursts (e.g. `-1Hz [-8, 7] (14, 3.9)`) plus overrun and not-found counters — the average is the error to feed to other SDR tools as the ppm correction.

### Seed a rough offset when the dongle is far off

Re-centre the measurement window when the oscillator error is too large for a cold lock.

```
kal -c 131 -e 25
```

Outcome: the same offset calculation, but the search window is first shifted by the supplied 25 ppm — use when a plain run reports every burst as `not found`, then re-run without `-e` once the true offset is within range.

*No corpus evidence — derived from upstream documentation.*
