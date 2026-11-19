---
name: "btscanner"
categories:
  - blackarch-bluetooth
version: "2.1"
homepage: "http://www.pentest.co.uk"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2018-12-05"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# btscanner

> Ncurses Bluetooth scanner that runs inquiry and brute-force address scans and keeps a persistent per-device store.

## What it's for

Batch discovery and fingerprinting of classic Bluetooth devices from a terminal, written by Tim Hurman (pentest.co.uk, 2004). It offers two scan types: an inquiry scan that finds discoverable devices, and a brute-force scan that steps through the address space probing each BD address in turn, which also surfaces non-discoverable devices. Every device found is written to a persistent store under `~/bts/<BDADDR>/` (info plus first/last-seen timestamps), so repeat visits show when a device was in range. It is a BlueZ-era classic Bluetooth tool: no BLE support, and the XML configuration must validate against the bundled DTD or the program refuses to start.

## Typical use cases

### Run inquiry and brute-force scans

```
sudo btscanner
```

Outcome: opens the ncurses UI and resets the adapter(s); press `i` to start an inquiry scan for discoverable devices or `b` to start a brute-force address scan. Discovered devices appear in a scrollable list with name, class, and manufacturer (resolved from the bundled IEEE OUI list); press Enter on a selection to probe it for full details with a live RSSI/link-quality meter (this can trigger a pairing prompt on the peer). `a` aborts running scans, `s` dumps a summary log of everything seen since the last summary, and `Q` quits.

### Scan without resetting the adapter

```
sudo btscanner --no-reset
```

Outcome: skips the dongle reset btscanner normally performs before each scan — for adapters that crash or hang when reset repeatedly mid-session; scanning then starts immediately via `i` or `b`.

### Point it at an alternate configuration file

```
sudo btscanner --cfg=/etc/btscanner/btscanner.xml
```

Outcome: parses and validates the given XML configuration (scan settings, log paths, store directory) against the btscanner DTD instead of the installed default; if validation fails, btscanner exits with an error rather than scanning.

*No corpus evidence — derived from upstream documentation.*
