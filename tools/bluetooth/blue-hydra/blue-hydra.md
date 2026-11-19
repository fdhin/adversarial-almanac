---
name: "blue-hydra"
categories:
  - blackarch-bluetooth
version: "710.1c2372d"
homepage: "https://github.com/pwnieexpress/blue_hydra"
packaged-by: [blackarch, kali]
blackarch-builddate: "2024-08-09"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "pwnieexpress/blue_hydra"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# blue-hydra

> Long-running Bluetooth device discovery service that tracks classic and BLE devices over time into a SQLite database, with optional Ubertooth support.

*No corpus evidence — derived from upstream documentation (the upstream repository has since been removed from GitHub; the archived README is the source).*

## What it's for

BlueHydra (Pwnie Express) is built for exactly one job: sitting on a sensor for days and building a history of every Bluetooth device that passes through range — both classic and Bluetooth Low Energy, including devices not in discoverable mode when an Ubertooth One is attached. Unlike a one-shot scanner it maintains per-device state over time in a SQLite database, so you can ask "what was seen, when, and how strong" rather than just "what is visible now". It runs as a plain script or daemonizes, and on Pwnie Express sensors it shipped as a system service feeding the Pulse dashboard. Runtime options live in `blue_hydra.yml` next to the script (adapter selection via `bt_device`, scan rate via `info_scan_rate`, RSSI logging, filtered/raw btmon logging); it requires bluez 5 and Ruby ≥ 2.1. Recommended hardware is the Sena UD100 adapter, optionally plus an Ubertooth One — which extends detection but does not replace a conventional dongle.

## Typical use cases

### Watch the vicinity and build a device history

```
sudo ./bin/blue_hydra
```

Outcome: starts live discovery on the configured adapter with a console UI listing devices as they are seen (vendor, name, type, LE advertisements); every observation is appended to the SQLite database (`blue_hydra.db`) in the blue_hydra directory and to `blue_hydra.log`. Leave it running for the duration of the survey, then query the database for per-device first/last-seen and RSSI history. If gems complain, prefix with `bundle exec`.

### Run unattended as a sensor daemon

```
sudo ./bin/blue_hydra -d
```

Outcome: suppresses the console UI and forks to the background, continuing to log devices to the database and log files — the deployment shape for a Raspberry-Pi-class sensor left in place. `-z` runs the same job with MAC addresses masked on screen for demos, `--no-db` keeps everything in RAM only, and `--help` lists the remaining options. Stop the daemon by killing the process; results survive in the database.
