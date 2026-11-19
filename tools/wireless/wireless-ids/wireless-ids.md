---
name: "wireless-ids"
categories:
  - blackarch-wireless
version: "24.b132071"
homepage: "https://github.com/SYWorks/wireless-ids"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "SYWorks/wireless-ids"
github-stars: 276
github-pushed: "2014-02-26"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wireless-ids

> Console-based wireless IDS that flags WEP/WPA/WPS attacks, rogue APs and evil twins while sniffing monitor-mode traffic.

## What it's for

Wireless IDS (by SY Chua / SYWorks, 2014) is a Python console application that watches surrounding 802.11 traffic for attack signatures: mass deauthentication (handshake-capture attacks), WEP attacks (broadcast-MAC data floods, Korek chopchop, fragmentation PRGA), WPS brute force in the style of Reaver/WPSCrack, MDK3 attacks (WPA downgrade, TKIP Michael shutdown exploitation, beacon flooding, authentication DoS), association flooding and the WPA migration mode attack. It also surfaces rogue-AP indicators — access points answering probes for other SSIDs, similar-looking evil-twin SSIDs, and clients whose connections changed to a different AP — and shows the SSIDs devices are probing for. It is detection and alerting only: it takes no countermeasures, and it needs the operator to judge evil-twin candidates. The packaged command is a wrapper that runs `wids.py` from `/usr/share/wireless-ids/` under Python 2.

## Typical use cases

### Start an interactive monitoring session

Run the tool bare to get its console menus.

```
sudo wireless-ids
```

Outcome: opens the interactive console; you pick the capture interface from a menu and it begins continuously classifying observed attack events, probing devices and SSID lookups (vendor OUIs come from the bundled `mac-oui.db`). Running as root is required.

### Capture and analyse a fixed window

Skip the interactive menus by supplying the interface and a capture duration on the command line.

```
sudo wireless-ids -i wlan0mon -t 120
```

Outcome: captures traffic on `wlan0mon` for 120 seconds (`-t`, in seconds) and then reports the suspicious activities detected in that window.

### Monitor while hiding probe noise

Suppress the constant listing of probing devices so alerts stand out.

```
sudo wireless-ids -i wlan0mon -t 120 -hp
```

Outcome: same capture-and-analyse run, but stations' probe-request SSIDs are no longer displayed; deauthentication, WEP, WPS and rogue-AP alerts remain visible.

*No corpus evidence — derived from upstream documentation.*
