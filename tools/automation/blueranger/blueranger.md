---
name: "blueranger"
categories:
  - blackarch-automation
version: "1.1"
homepage: "https://www.hackfromacave.com/projects/blueranger.html"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-12-03"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/blueranger"
github-stars: 1
github-pushed: "2025-12-03"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# blueranger

> Bash script that physically locates a Bluetooth device by continuously L2pinging it and watching the reported Link Quality as you move.

## What it's for

BlueRanger (JP Dunning, hackfromacave.com) is a simple proximity tool for finding hidden Bluetooth devices — headsets, badges, phones. It continuously L2CAP-pings a known Bluetooth address through a chosen HCI interface and displays the Link Quality value your adapter reports for the link, plus a running proximity indicator and ASCII range bar; as you physically approach the device the link quality climbs, so walking around while watching the bar homes you in on it. It is a locating aid, not a scanner or pairing tool: you must already know the target's BD_ADDRESS (from `hcitool scan` or similar), and it requires the BlueZ stack (hciconfig/l2ping) and a working Bluetooth adapter. An external directional antenna makes ranging much more precise. The original hackfromacave release is packaged in a modernized mirror repo; version and behavior are unchanged.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Range in on a known device

Find where a specific Bluetooth device is physically hiding.

```
blueranger hci0 00:11:22:33:44:55
```

Outcome: after a `Starting ...` banner the script prints the ping count, the proximity change, and the current Link Quality (e.g. `FOUND 255/255`) with an ASCII range bar; the bar grows as you carry the machine closer to `00:11:22:33:44:55` and shrinks as you move away. Close it with two Ctrl+C presses. Select a different adapter (e.g. one with an external antenna) by passing its name as the first argument, such as `hci1`.
