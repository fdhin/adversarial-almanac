---
name: "gpredict"
categories:
  - blackarch-radio
version: "1764.1d80049"
homepage: "http://gpredict.oz9aec.net/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "csete/gpredict"
github-stars: 1201
github-pushed: "2026-11-01"
github-archived: false
github-issues: 122
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 245
gh-activity-checked: "2026-11-09"
---

# gpredict

> Real-time satellite tracking and orbit prediction for the desktop.

## What it's for

Gpredict (by Alexandru Csete, OZ9AEC) tracks satellites and predicts passes using the SGP4/SDP4 propagation algorithms with NORAD two-line element sets. The GTK application displays tracking data as lists, maps and polar plots (any combination, many modules at once), predicts upcoming passes for any number of ground stations, and runs in real time, simulated time or manual control. It also closes the loop on hardware: Doppler tuning of radios via Hamlib rigctld and antenna rotator control via rotctld, making it the coordination point of a satellite ground station. TLE data is fetched automatically from online sources such as Celestrak; daily use is through the GUI, with only housekeeping flags on the command line.

## Typical use cases

### Start fullscreen

```
gpredict --fullscreen
```

Outcome: opens the main window covering the whole screen — the usual setup for a dedicated pass-tracking display during a satellite contact.

### Force a clean TLE reload

```
gpredict --clean-tle
```

Outcome: clears the cached two-line element data in the user's gpredict configuration directory and starts the application; fresh elements are fetched from the configured sources, curing stale-orbit symptoms after a long offline period.

### Reset transponder data

```
gpredict --clean-trsp
```

Outcome: clears the cached transponder database (satellite uplink/downlink mode definitions) so gpredict rebuilds it from its update sources at startup — the fix when transponder lists show wrong or missing entries.

*No corpus evidence — derived from upstream documentation.*
