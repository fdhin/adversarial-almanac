---
name: "hashcatch"
categories:
  - blackarch-wireless
version: "52.8145660"
homepage: "https://github.com/staz0t/hashcatch/releases"
packaged-by: [blackarch]
blackarch-builddate: "2020-03-21"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "staz0t/hashcatch"
github-stars: 798
github-pushed: "2021-05-19"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hashcatch

> Automated wardriving capture: walks every nearby WiFi network, deauthenticates its clients and banks the WPA handshakes.

## What it's for

hashcatch (by Sivanesh Ashok, staz0t) is a single-command handshake harvester: it repeatedly hops across all WiFi networks in range, deauthenticates their connected clients and waits for the reconnection handshakes, saving each captured handshake and recording the network in a small database. It is built for opportunistic, always-on collection on portable Linux devices (Raspberry Pi, NetHunter phones) rather than targeted attacks on one network — it is not a cracker itself; feed the captured handshakes to a cracking tool afterwards. It deliberately skips PMKID and WPS attacks to stay fast per AP, and needs a monitor-mode-capable card plus aircrack-ng, hcxtools and hashcat-utils installed.

## Typical use cases

### One-time setup

```
sudo hashcatch --setup
```

Outcome: interactive setup that detects wireless interfaces, has you pick the one to use, and writes `/etc/hashcatch/hashcatch.conf`; run once after install or after changing hardware.

### Start continuous capture

```
sudo hashcatch
```

Outcome: puts the card to work cycling through every nearby network — deauthenticating clients and capturing reconnection handshakes — and runs until interrupted with Ctrl-C. Captured handshakes land in `/usr/share/hashcatch/handshakes/` and each network's BSSID/ESSID is appended to `/usr/share/hashcatch/db`; with an Internet connection, approximate geolocation of the AP is added to the db as well. Spend 20–30 seconds in range of a network you care about to reliably catch its handshake.

### Exclude networks from capture

```
interface=wlan1
ignore=HomeNetwork,PhoneHotspot
```

Outcome: editing `/etc/hashcatch/hashcatch.conf` so the `ignore` field lists ESSIDs (comma-separated, no spaces) makes hashcatch skip those networks entirely — e.g. your own AP and tether — while the `interface` field pins the capture card; no spaces around the `=` or within the list.

*No corpus evidence — derived from upstream documentation.*
