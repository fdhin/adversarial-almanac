---
name: "netattack"
categories:
  - blackarch-wireless
version: "24.230b856"
homepage: "https://github.com/chrizator/netattack2"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "chrizator/netattack2"
github-stars: 460
github-pushed: "2018-01-31"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netattack

> Interactive Python script that scans local and wireless networks and launches ARP-spoof and deauth attacks from a menu.

*No corpus evidence — derived from upstream documentation.*

## What it's for

NETATTACK2 (packaged as netattack, by chrizator) is a terminal-menu tool written in Python 2 with scapy covering two jobs: scanning — live hosts with operating-system detection and open ports, plus nearby access points with encryption and WPS details — and attacking what it finds: ARP spoofing with DNS-query sniffing, kicking hosts offline, and Wi-Fi deauthentication, including a "deauth-all" mode that re-scans and floods every network in range periodically. It enables monitor mode on the chosen card itself. It is unmaintained (2017-18 era, Python 2) and offers no scriptable interface — everything happens through its prompts.

## Typical use cases

### Run the attack menu

Launch the interactive scanner/attacker.

```
sudo netattack
```

Outcome: offers to auto-install missing Python modules on first run, then shows the numbered menu — pick an interface (monitor mode is enabled for Wi-Fi attacks), scan hosts or APs, and select the spoof, kick, or deauth action against listed targets. An upstream source tree runs the same program as `sudo ./netattack2.py`.
