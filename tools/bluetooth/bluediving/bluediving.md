---
name: "bluediving"
categories:
  - blackarch-bluetooth
version: "0.9"
homepage: "http://bluediving.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2023-08-07"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "balle/bluediving"
github-stars: 132
github-pushed: "2020-02-12"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bluediving

> Menu-driven Perl suite for 2000s-era Bluetooth phone auditing: wraps the BlueBug, BlueSnarf, BlueSnarf++ and BlueSmack attacks with scanners and helper tools in one console.

*No corpus evidence — derived from upstream documentation.*

## What it's for

bluediving (the packaged 0.9 release is the bluedivingNG generation by Bastian Ballmann) collects the classic Bluetooth attacks of its era behind a single interactive console: it scans for discoverable devices, enumerates their services, and then lets you run exploits — BlueBug command injection, BlueSnarf/BlueSnarf++ file retrieval (including the Sony Ericsson variant), BlueSmack L2CAP ping-of-death — plus an RFCOMM scanner, an L2CAP packet generator and wrappers for tools like redfang and carwhisperer. Tool paths, channels and payloads are set in `bluedivingNG.conf` (`/etc/bluedivingNG.conf` on packaged installs). It targets handsets from the mid-2000s; on anything modern the interesting part is usually the scanning and RFCOMM enumeration, since the exploits depend on long-fixed firmware bugs. Needs bluez, and root for nearly everything.

## Typical use cases

### Run the suite interactively against one adapter

```
sudo bluedivingNG.pl hci0
```

Outcome: opens the menu-driven console on adapter `hci0`; the scan entry populates a target list (address, name, device class), and selecting a target unlocks the attack menu — bluesnarf, bluebug, bluesmack, RFCOMM scanning — each printing its progress and results inline. With two or more adapters available its "greenplaque" scanning mode alternates scanning across the radios; with one it falls back to plain `hcitool` scanning.
