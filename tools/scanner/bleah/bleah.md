---
name: "bleah"
categories:
  - blackarch-scanner
version: "53.6a2fd3a"
homepage: "https://github.com/evilsocket/bleah"
packaged-by: [blackarch]
blackarch-builddate: "2019-01-15"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "evilsocket/bleah"
github-stars: 1093
github-pushed: "2019-02-19"
github-archived: true
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bleah

> Bluetooth Low Energy scanner and GATT enumerator for "smart" devices — deprecated by its author in favour of bettercap's BLE modules.

## What it's for

Bleah (by Simone 'evilsocket' Margaritelli) scans for Bluetooth Low Energy advertisements, connects to devices, enumerates their GATT services and characteristics, and can write arbitrary data to a chosen characteristic — the classic workflow for poking at "smart" locks, bulbs, and wearables in a lab. It builds on the bluepy helper and needs root. The repository is archived and its README now points users to bettercap, whose BLE modules cover the same ground and are maintained; the usage below documents the last released CLI as preserved by the archived project page.

## Typical use cases

### Scan continuously for BLE devices

```
sudo bleah -t0
```

Outcome: scans indefinitely (timeout 0) on the default HCI device, printing every BLE device seen with its address and advertised name; `-i hci1` picks another adapter and `-s -80` sets a dBm sensitivity threshold.

### Enumerate a specific device

```
sudo bleah -b "aa:bb:cc:dd:ee:ff" -e
```

Outcome: connects to that device and dumps its full services/characteristics tree; add `-f` to try connecting even when the device does not advertise itself as connectable.

### Write to a characteristic

```
sudo bleah -b "aa:bb:cc:dd:ee:ff" -u "c7d25540-31dd-11e2-81c1-0800200c9a66" -d "hello world"
```

Outcome: writes the given bytes to that characteristic UUID on that device; `-r data.bin` takes the payload from a file instead of the command line.

*No corpus evidence — derived from the archived upstream project page (2018 snapshot); the archived master branch no longer carries the source.*
