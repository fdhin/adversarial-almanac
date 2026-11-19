---
name: "openwebrx"
categories:
  - blackarch-radio
version: "1.2.2.r389.g640c5b0"
homepage: "https://github.com/jketterl/openwebrx"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: AGPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "jketterl/openwebrx"
github-stars: 1339
github-pushed: "2024-12-11"
github-archived: false
github-issues: 73
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# openwebrx

> Multi-user SDR receiver with a browser interface — one attached radio, many simultaneous listeners.

*No corpus evidence — derived from upstream documentation.*

## What it's for

OpenWebRX turns a Linux host with an SDR device into a web receiver: it runs demodulators server-side (csdr-based AM/FM/SSB/CW and digiham-based DMR/YSF/Pocsag/D-Star/NXDN, plus wsjt-x modes like FT8/WSPR) and serves an HTML5 waterfall that several users can tune independently at the same time. Hardware support covers the common RTL-SDR/SDRplay/Airspy/PlutoSDR class of devices, and several receivers can be hosted on one instance. It is receive-only — there is no transmit path — and configuration (device, decimation, bookmarks) lives in the classic `openwebrx.conf` / `config_webrx.py` files shipped with the package.

## Typical use cases

### Start the receiver in the foreground

```
openwebrx
```

Outcome: the receiver initializes the configured SDR device and serves the web interface on the configured port (default 8073); opening `http://localhost:8073` in a browser shows the waterfall, and every connected user gets an independent VFO.

### Run it as a system service

```
systemctl enable --now openwebrx
```

Outcome: the shipped `openwebrx.service` unit starts the receiver at boot and keeps it running; check `systemctl status openwebrx` for the port and device state, and edit the configuration files followed by a service restart to change hardware or frequencies.
