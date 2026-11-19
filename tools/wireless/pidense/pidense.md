---
name: "pidense"
categories:
  - blackarch-wireless
version: "29.ef26704"
homepage: "https://github.com/WiPi-Hunter/PiDense"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "WiPi-Hunter/PiDense"
github-stars: 567
github-pushed: "2020-02-01"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pidense

> Python 2 monitor that channel-hops a monitor interface and flags rogue-AP tactics — same-SSID/different-encryption twins, blacklist SSIDs, KARMA and WiFi Pineapple activity.

## What it's for

PiDense (one of the WiPi-Hunter family of tools) watches 802.11 beacon and probe-response traffic through scapy and raises color-coded threats on a terminal: an SSID suddenly broadcast with different encryption (the classic evil-twin/KARMA signature), SSIDs on your blacklist appearing unencrypted, KARMA-style same-SSID-multiple-BSSID patterns, and a running density figure for unencrypted networks in range. It is a detection aid for spotting WiFi Pineapple and fake-AP activity around you, not an attacker — it sends nothing. Its interface is deliberately minimal to the point of spartan: the script takes no command-line options at all, hardcodes the monitor interface (`wlan0mon` — edit line in `__main__` to change it), reads SSIDs from a `blacklist.txt` file in the working directory, and logs findings to `/var/log/pidens.log`. Development stopped in 2020 and it is Python 2 with old scapy, so expect to run it on an older environment.

## Typical use cases

### Run the rogue-AP watch loop

Start it on a monitor interface and watch the threat table.

```
cd PiDense && sudo python2 pidense.py
```

Outcome: hops channels 1-13 in ~45-second cycles, printing a severity/attack-type/content THREATS table as detections fire (KARMA patterns, blacklist hits, same-SSID-different-encryption, unencrypted network density) and appending each finding to `/var/log/pidens.log`; the interface must already be in monitor mode (e.g. `airmon-ng start wlan0`) and `blacklist.txt` must sit next to the script.

### Watch for evil twins of your own SSIDs

Curate the blacklist so clones of your networks stand out.

```
echo OfficeNet >> blacklist.txt
sudo python2 pidense.py
```

Outcome: any beacon carrying a blacklisted SSID that is unencrypted (or otherwise mismatched against the known-good copy) is printed at Critical severity with the offending SSID — the operational core of the tool, since a twin AP is how captive-portal and KARMA attacks present themselves.

*No corpus evidence — derived from upstream documentation.*
