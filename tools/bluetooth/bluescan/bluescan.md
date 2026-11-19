---
name: "bluescan"
categories:
  - blackarch-bluetooth
version: "1.0.6"
homepage: "http://www.darknet.org.uk/2015/01/bluescan-bluetooth-device-scanner/"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-24"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# bluescan

> Interactive bash wardriving script that loop-scans for discoverable Bluetooth devices and logs address, name and manufacturer with timestamps.

*No corpus evidence — derived from upstream documentation (the packaged upstream archive, bluescan 1.0.6 by ~aKira~, is the reference).*

## What it's for

bluescan is a single-script Bluetooth wardriving tool ("Wardriver para Bluetooth", by ~aKira~, 2007) with a Spanish-language console interface. It takes no arguments and drives bluez directly: it picks the first available adapter from `hciconfig`, repeatedly runs `hcitool scan` to catch discoverable devices as they come and go, and enriches each hit with `sdptool browse` output to guess the manufacturer (for example by recognizing Nokia-specific services). Per-device timestamps make it usable from a moving laptop or a static window position. It is deliberately minimal — no BLE support, no attack features — and pairs with the same era of bluez-utils it was written against; on modern systems the `hciconfig`/`sdptool` layer must still be installed for it to find anything.

## Typical use cases

### Run a hands-off wardriving sweep

```
sudo ./bluescan
```

Outcome: after a banner showing the local adapter's BD address, the script scans in a loop and prints each newly discovered device with its name, inferred manufacturer and detection time; duplicate sightings of the same address are collapsed into a running count. Press Ctrl+C to stop: it reports the total number of distinct devices seen, then offers to print the session log on screen, export it to `~/bslog.txt`, or exit.
