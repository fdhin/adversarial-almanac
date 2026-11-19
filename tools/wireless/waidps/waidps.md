---
name: "waidps"
categories:
  - blackarch-wireless
version: "16.ff8d270"
homepage: "https://github.com/SYWorks/waidps"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "SYWorks/waidps"
github-stars: 517
github-pushed: "2017-10-22"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# waidps

> Menu-driven Python 2 console that audits Wi-Fi networks, watches for attacks, and captures WPA handshakes.

## What it's for

WAIDPS (Wireless Auditing, Intrusion Detection & Prevention System) by SYWorks is a single-file Python 2 console application that combines several wireless-audit jobs in one interactive menu: access point and station discovery with live monitoring, an intrusion-detection view that flags suspicious activity (MDK3-style beacon flooding, authentication DoS, WIDS/WIPS confusion, WPA downgrade tests, rogue APs), WPA/WPA2 handshake capture with built-in dictionary cracking, and WPS PIN auditing against detected networks. It also keeps session logs and supports whitelist/suspicious-activity lists. It takes no command-line flags — everything is done from numbered menus. It is a Python 2 era tool, effectively unmaintained since 2017, so it needs a working python2 with pycryptodome and a monitor-capable wireless interface.

## Typical use cases

### Launch the auditing and monitoring console

Start the interactive tool on a machine with a monitor-capable Wi-Fi card.

```
sudo waidps
```

Outcome: opens the Wireless Network Auditing Main Menu (installed as `/usr/bin/waidps`, wrapping `waidps.py`); from there select the interface, scan for APs/stations, open Live Access Point Monitoring, the Intrusion Detection view, or the WPA/WPA2/WPS auditing menus (key in `Help` inside the tool for the option list). Captured handshakes and session logs are listed and can be reviewed from the same menus.

*No corpus evidence — derived from upstream documentation (repo source `waidps.py` and BlackArch packaging).*
