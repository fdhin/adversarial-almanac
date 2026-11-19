---
name: "wifi-honey"
categories:
  - blackarch-honeypot
version: "1.0"
homepage: "http://www.digininja.org/projects/wifi_honey.php"
packaged-by: [blackarch, kali]
blackarch-builddate: "2018-11-12"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "samothrakes/wifi-honey"
github-stars: 9
github-pushed: "2016-12-30"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wifi-honey

> Management script that builds a four-access-point WiFi honeypot from one card and supervises the whole deployment in a labelled screen session.

## What it's for

wifi_honey (Robin Wood, DigiNinja) automates the tedious setup of a wireless honeypot: given a single monitor-mode interface it creates five virtual monitor interfaces — four are configured as access points broadcasting variations of the target ESSID, and the fifth runs airodump-ng. A client that auto-connects to a remembered network finds one of the fake APs and associates; for WPA/WPA2 networks, the capture interface records the first two packets of the four-way handshake as a bonus, giving you material to attack the real key offline. Everything runs inside one screen session with labelled windows per AP and the sniffer, so you can flip between them while watching for victims. It requires `screen` and the aircrack-ng suite, and it must run from a directory containing `wifi_honey_template.rc` (installed under `/etc/wifi-honey/` by the packaged build) or it fails when generating the screen resource file. It automates deployment only — it never cracks or analyzes anything itself.

## Typical use cases

### Spin up the honeypot fleet

Deploy the multi-AP honeypot for a chosen ESSID.

```
sudo wifi-honey fake_wpa_net
```

Outcome: the script creates the five monitor-mode interfaces, brings up four fake APs beaconing fake_wpa_net plus one airodump-ng capture interface, all inside a single screen session with labelled windows — switch windows to watch each AP and the handshake capture.

### Pin the channel and base interface

Control exactly where and on what hardware the fleet runs.

```
sudo wifi-honey fake_wpa_net 6 wlan1mon
```

Outcome: same deployment, but the fake APs are pinned to channel 6 and derived from the wlan1mon monitor interface instead of auto-detected defaults — the documented three-argument form (ESSID, channel, interface).

*No corpus evidence — derived from upstream documentation.*
