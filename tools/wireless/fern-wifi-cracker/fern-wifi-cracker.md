---
name: "fern-wifi-cracker"
categories:
  - blackarch-wireless
version: "303.58ed54e"
homepage: "https://github.com/savio-code/fern-wifi-cracker"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-04-05"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "savio-code/fern-wifi-cracker"
github-stars: 1620
github-pushed: "2026-04-11"
github-archived: false
github-issues: 171
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# fern-wifi-cracker

> PyQt5 GUI that automates WEP/WPA/WPS key recovery and other wireless attacks through aircrack-ng and friends.

## What it's for

Fern Wifi Cracker (Savio D'Souza) is a Python/Qt5 graphical front end for wireless security auditing. It drives aircrack-ng, reaver and related tools to recover WEP keys (fragmentation, chop-chop, Caffe-Latte, Hirte, ARP request replay), attack WPA/WPA2 with dictionaries or WPS, and store recovered keys in a local database. Beyond key cracking it bundles session hijacking, access point geolocation, an internal MITM engine, and brute-force attacks against HTTP, HTTPS, TELNET and FTP. It is a desktop GUI with no scriptable interface — everything happens through its windows, so it does not fit headless or automated workflows; for those, use the underlying tools directly.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Launch the auditing GUI

Open the main window to pick a wireless interface and start scanning and attacking networks.

```
fern-wifi-cracker
```

Outcome: opens the Fern main window on the desktop (the packaged launcher runs `execute.py` from the install directory); from there you select the interface, scan for access points, and launch the WEP/WPA/WPS attacks. Requires a root session and a wireless interface the backends can put into monitor mode.
