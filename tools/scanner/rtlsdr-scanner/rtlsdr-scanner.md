---
name: "rtlsdr-scanner"
categories:
  - blackarch-scanner
version: "v1.3.2.r1.g3c032de"
homepage: "https://github.com/EarToEarOak/RTLSDR-Scanner"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "EarToEarOak/RTLSDR-Scanner"
github-stars: 724
github-pushed: "2018-12-05"
github-archived: false
github-issues: 27
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rtlsdr-scanner

> Cross-platform Python (wxPython) GUI that sweeps frequency ranges with an RTL-SDR dongle and plots the spectrum.

## What it's for

A frequency-scanning front end for RTL-SDR hardware (Al Brown, Ear to Ear
Oak): instead of tuning to one frequency, it steps across a start/stop range
you define, sampling each step for a chosen dwell time, and renders the
combined spectrum so you can see every signal in the band and how strong it
is. Typical purposes are surveying a band before choosing operating
frequencies, hunting for intermittent transmitters, and comparing spectrum
usage over time (saved scans can be loaded side by side). It is a GUI
application (Python 2.7 era, wxPython + matplotlib), not a headless scanner —
there is no batch or CLI scan mode; it also has no demodulation, it only shows
energy per frequency. GPS support can geo-tag sweeps, and scans export to CSV.

## Typical use cases

### Start the scanner GUI

Launch the application with a dongle attached.

```
python2 -m rtlsdr_scanner
```

Outcome: the main window opens; set Start and Stop frequencies, Mode (single
or continuous sweep), Dwell time per step and FFT size, then Scan → Start —
the spectrum of the swept range is drawn live, and the dongle gain,
calibration and sample-band settings live under Edit → Preferences.

### Load a previous scan for comparison

Re-open a saved sweep at startup.

```
python2 -m rtlsdr_scanner survey.rfs
```

Outcome: the GUI starts with `survey.rfs` loaded (scans are saved as `.rfs`
files); use Tools → Compare to load a second saved scan and overlay the two to
see what appeared or disappeared between sweeps, and File → Export to dump a
scan to CSV for external analysis.

### Diagnose missing dependencies

Check that the Python stack is complete before blaming the dongle.

```
python2 rtlsdr_scanner/rtlsdr_scan_diag.py
```

Outcome: the bundled diagnostic script checks for the required modules
(wxPython, matplotlib, numpy, Pillow, pyrtlsdr, pyserial) and reports which are
missing, so installation problems surface as an explicit list instead of an
import error at startup.

*No corpus evidence — derived from upstream documentation (EarToEarOak
RTLSDR-Scanner README and the packaged source tree).*
