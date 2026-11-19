---
name: "howmanypeoplearearound"
categories:
  - blackarch-recon
version: "123.b05e06a"
homepage: "https://github.com/schollz/howmanypeoplearearound"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "schollz/howmanypeoplearearound"
github-stars: 7091
github-pushed: "2024-08-17"
github-archived: false
github-issues: 27
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# howmanypeoplearearound

> Estimates how many people are nearby by counting smartphones from sniffed WiFi probe requests.

## What it's for

howmanypeoplearearound (schollz) puts a monitor-mode WiFi adapter to work sniffing probe requests with tshark, treats each distinct phone as a proxy for one person, and prints an estimated headcount. Typical uses are foot-traffic monitoring on networks you own, for example with a Raspberry Pi sensor. It does not locate or identify people — MAC addresses are only counted and vendor-labeled — and monitoring networks you do not own may be illegal in many jurisdictions.

## Typical use cases

### Quick headcount on your own network

Scan for a fixed period and get the estimate.

```
howmanypeoplearearound -a wlan1 -s 60
```

Outcome: the adapter is flipped to monitor mode, probe requests are collected for 60 seconds with a progress bar, then a line like `There are about 3 people around.` is printed.

### Record the devices seen as JSON

Keep the per-device detail, not just the count.

```
howmanypeoplearearound -a wlan1 -o phones.json
```

Outcome: writes `phones.json` with one entry per detected phone (`rssi`, `mac`, vendor `company`), e.g. `{"rssi": -49.0, "mac": "ac:37:43:...", "company": "Apple, Inc."}` — a higher RSSI means physically closer.

### Continuous monitoring with plots

Loop the capture and later visualize occupancy over time.

```
howmanypeoplearearound -a wlan1 -o test.json --loop
```

Outcome: appends newly seen devices to `test.json` until interrupted; a follow-up `howmanypeoplearearound --analyze test.json` generates an `index.html` and serves interactive plots (people over time, RSSI per device) at http://localhost:8001.

*No corpus evidence — derived from upstream documentation.*
