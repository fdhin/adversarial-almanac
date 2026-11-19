---
name: "mitmap"
categories:
  - blackarch-wireless
version: "89.b590f9a"
homepage: "https://github.com/xdavidhu/mitmAP"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "xdavidhu/mitmAP"
github-stars: 1693
github-pushed: "2019-11-03"
github-archived: true
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mitmap

> Interactive script that assembles a rogue access point with SSL stripping, DNS spoofing, and traffic capture.

*No corpus evidence — derived from upstream documentation.*

## What it's for

mitmAP (packaged as mitmap, by xdavidhu) is a Python script that asks a short series of questions and then builds a complete evil-twin stack on Kali-style systems: a hostapd access point with internet bridging, DHCP, SSLstrip2 for HSTS bypass, plus optional mitmproxy, Driftnet image capture, TShark pcap capture, DNS spoofing, and a configurable client speed limit, saving results to files. It orchestrates other tools rather than implementing attacks itself, and expects root with one wired and one wireless interface (or two wireless cards). The project is archived and no longer maintained.

## Typical use cases

### Launch the rogue AP builder

Start the guided setup and bring up the attack AP.

```
sudo mitmap
```

Outcome: on first run asks whether to install dependencies and create the config files (answer y to both); then interactively asks for interfaces, SSID and channel, brings up the access point, and enables the chosen capture and spoofing components, writing captured data to files in the working directory. An upstream source tree runs the same program as `sudo python3 mitmAP.py` (`sudo python3 mitmAP_rpi.py` on Raspberry Pi).
