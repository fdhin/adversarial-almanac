---
name: "spektrum"
categories:
  - blackarch-radio
version: "2.1.0"
homepage: "https://github.com/pavels/spektrum"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-12"
build-system: scripts
license: BSD-3
meta-checked: "2026-11-09"
github-repo: "pavels/spektrum"
github-stars: 707
github-pushed: "2022-11-19"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spektrum

> GUI spectrum analyzer for RTL-SDR with wide frequency sweeps, cursors and relative-mode measurements.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Spektrum is a desktop spectrum analyzer that drives an RTL-SDR dongle. Its distinguishing feature is sweeping across a large frequency span (not just the dongle's instantaneous bandwidth), with measurement tooling around the trace: dual frequency/amplitude cursors with delta readouts, min/max/median hold, video averaging, reference trace storage and offset, IF/upconverter offset support, and a relative mode that zeros the display against a stored reference — the classic workflow for measuring filter characteristics or antenna VSWR against a noise source. The UI is built on Processing; the packaged Linux build wraps the released binary behind a `spektrum` launcher script. It is receive/measure only.

## Typical use cases

### Start the analyzer

```
spektrum
```

Outcome: the GUI opens and claims the first RTL-SDR device; set the start/stop frequencies of the sweep, adjust tuner gain, and the swept trace renders continuously. For VSWR/filter work, switch on relative mode with a noise source connected, capture the reference, then swap in the antenna or filter under test and read the difference with the cursors.
