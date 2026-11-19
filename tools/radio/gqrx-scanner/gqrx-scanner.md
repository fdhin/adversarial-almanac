---
name: "gqrx-scanner"
categories:
  - blackarch-radio
version: "1.0.5"
homepage: "https://github.com/neural75/gqrx-scanner"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-10-31"
build-system: cmake
license: MIT
meta-checked: "2026-11-09"
github-repo: "neural75/gqrx-scanner"
github-stars: 306
github-pushed: "2026-09-05"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 42
gh-activity-checked: "2026-11-09"
---

# gqrx-scanner

> Frequency scanner that drives the gqrx SDR receiver over its remote-control protocol.

## What it's for

gqrx-scanner automates scanning for gqrx: it talks to a running gqrx instance through the gqrx remote protocol (TCP, default port 7356 — enable Tools → Remote Control in gqrx first) and either sweeps a frequency range or walks gqrx's bookmarked frequencies, stopping on any signal that opens the squelch. An adaptive algorithm prioritizes active stations, fine-tunes onto the exact carrier, and supports interactive skip/ban/pause keys; detected signals can be recorded, and voice activity detection (VOX, Linux/PipeWire) lets the scanner hang up when a conversation ends rather than when the carrier drops — the right behavior on digital modes with constant carriers. It is only the scanning brain: gqrx remains the receiver.

## Typical use cases

### Sweep a band

```
gqrx-scanner -m sweep -b 144000000 -e 148000000
```

Outcome: repeatedly sweeps 144–148 MHz, fine-tunes onto any carrier that opens gqrx's squelch and locks on it; during the scan, space/enter skips the current frequency, `b` bans a ~10 kHz window around it, `c` clears bans and `p` pauses.

### Scan only bookmarked frequencies

```
gqrx-scanner -m bookmark -t "Amateur|Marine"
```

Outcome: steps through the gqrx bookmarks whose tags match `Amateur` or `Marine` (case-insensitive, partial match) instead of sweeping — the better mode on cluttered bands.

### Scan around a center frequency

```
gqrx-scanner -f 145500000
```

Outcome: sweeps ±1 MHz around 145.5 MHz (center and span instead of explicit bounds), self-adjusting scan speed to the band activity.

### Scan a remote gqrx instance

```
gqrx-scanner -h target.example.com -p 7356
```

Outcome: connects to a gqrx running on another host rather than localhost, so a headless receiver box can be scanned from a workstation.

### Record detections and skip dead carriers by voice

```
gqrx-scanner -m sweep -b 118000000 -e 137000000 -r --vox -l 1000:5000
```

Outcome: sweeps the airband, records each detected signal to a dated audio file, probes new carriers for 1 s of speech before staying, and hangs up 5 s after voice stops (VOX requires PipeWire) — noise and data carriers no longer hold up the scan.

*No corpus evidence — derived from upstream documentation.*
