---
name: "wmbusmeters"
categories:
  - blackarch-radio
version: "1.17.1"
homepage: "https://github.com/wmbusmeters/wmbusmeters"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-11-10"
build-system: make
meta-checked: "2026-11-09"
github-repo: "wmbusmeters/wmbusmeters"
github-stars: 1358
github-pushed: "2026-11-06"
github-archived: false
github-issues: 74
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 998
gh-activity-checked: "2026-11-09"
---

# wmbusmeters

> Daemon and CLI that read wired M-Bus and wireless M-Bus meters and publish their readings as JSON, CSV, MQTT or REST calls.

*No corpus evidence — derived from upstream documentation.*

## What it's for

wmbusmeters acquires utility meter readings from wired m-bus and wireless wm-bus/OMS meters: it drives the common wmbus dongles (im871a, iu891a, amb8465, amb3665, cul, rc1180) and generic RTL-SDR receivers (via [[rtl-wmbus]]), decrypts and parses the telegrams, and turns them into human-readable lines, CSV, JSON, MQTT publishes, REST calls or log files. Meters are declared in per-meter config files so raw telegrams become named quantities ("MyTapWater 123.529 m³"). Run it once from the command line to test, or as a systemd daemon for continuous collection. It reads meters; it cannot command them (except polling C2/T2/S2/wired meters via configured bus aliases).

## Typical use cases

### Probe for a dongle and listen in the foreground

```
wmbusmeters auto:t1
```

Outcome: wmbusmeters scans serial devices every few seconds for any supported dongle (including an rtlsdr stick) and, once found, listens in T1 mode and prints each recognized meter's readings to the terminal — the recommended first test before committing to a fixed device path.

### Run continuously as a service

```
systemctl enable --now wmbusmeters
```

Outcome: the daemon starts at boot, reads `device=` and meter configuration from `/etc/wmbusmeters.conf` and the meters directory, and publishes decoded readings to the configured outputs; `killall -HUP wmbusmetersd` reloads configuration without a restart.

### Point the daemon at a private config directory

```
wmbusmetersd --useconfig=/home/user/.config/wmbusmeters /tmp/wmbusmeters.pid
```

Outcome: the daemon reads `wmbusmeters.conf` and meter files from the given directory instead of `/etc/wmbusmeters`, writing its pidfile to the second argument — how you run a second, isolated instance (e.g. one per dongle) alongside the system one.
