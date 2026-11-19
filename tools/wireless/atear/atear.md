---
name: "atear"
categories:
  - blackarch-wireless
version: "139.245ec8d"
homepage: "https://github.com/NORMA-Inc/AtEar"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "NORMA-Inc/AtEar"
github-stars: 328
github-pushed: "2022-09-01"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# atear

> Web-based wireless vulnerability assessment system: scan, pentest and monitor 802.11 networks from a browser.

## What it's for

AtEar (NORMA Inc) is a Flask web application that turns one wireless interface into a managed assessment sensor: it scans for ad-hoc stations, clients, APs and soft-APs; charts them by channel, encryption and type; runs automated WEP/WPA/WPA2 penetration tests; serves fake-AP phishing pages (Google, Facebook, Twitter lookalikes); and includes a WIDS that flags deauthentication/disassociation floods, association floods, TKIP/Chopchop-style attacks and rogue APs. It drives `aircrack-ng`, `hostapd`, `tshark` and friends underneath — you operate it through the browser, not the CLI. It is Python 2.7-era software from 2016–2022, best treated as a self-contained assessment appliance for a lab network rather than a current pentest toolkit.

## Typical use cases

### Start the assessment web UI

Launch the sensor and controller on a given wireless interface.

```
sudo atear --iface wlan0
```

Outcome: the wrapper starts AtEar's `run.py` against `wlan0` and serves the web UI on `http://127.0.0.1:8080`; scanning, charting, the automated WEP/WPA/WPA2 attacks, fake-AP phishing and the WIDS view are then driven from the browser.

*No corpus evidence — derived from upstream documentation.*
