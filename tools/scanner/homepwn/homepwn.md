---
name: "homepwn"
categories:
  - blackarch-scanner
version: "31.0803981"
homepage: "https://github.com/ElevenPaths/HomePWN"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ElevenPaths/HomePWN"
github-stars: 934
github-pushed: "2022-12-27"
github-archived: true
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# homepwn

> Telefónica's modular IoT pentesting console for discovering and auditing nearby devices over WiFi, Bluetooth Low Energy, NFC and SSDP/mDNS.

## What it's for

HomePWN ("Swiss Army Knife for Pentesting of IoT Devices", Ideas Locas / ElevenPaths) is an interactive Python console built on prompt_toolkit. Its discovery modules enumerate what is around you — WiFi scans with a monitor-mode adapter, BLE device scans, SSDP and mDNS service discovery — and its technology-specific modules then audit chosen targets over WiFi, BLE or NFC (BLE sniffing to pcap, spoofing, tag cloning). Requires root plus the relevant radios; the upstream repository is archived, so the packaged version is the final release.

## Typical use cases

### Launch the interactive console

Start the framework with the needed privileges.

```
sudo homepwn
```

Outcome: opens the HomePWN console (the upstream entry point is `sudo python3 homePwn.py`); from there list and run discovery modules to enumerate nearby devices, then load the matching technology-audit modules against a chosen target.

*No corpus evidence — derived from upstream documentation.*
