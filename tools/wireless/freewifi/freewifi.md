---
name: "freewifi"
categories:
  - blackarch-wireless
version: "30.1cb752b"
homepage: "https://github.com/kylemcdonald/FreeWifi"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "kylemcdonald/FreeWifi"
github-stars: 3007
github-pushed: "2018-02-20"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# freewifi

> Enumerate who else is on the wireless network you are joined to, ranked by traffic.

## What it's for

Kyle McDonald's FreeWifi repository is best known as a tutorial on getting internet access from public networks (private browsing windows, MAC address randomization with spoof-mac to reset portal time limits). The packaged tool is the repo's script `wifi-users.py`, installed as `freewifi`: it sniffs the wireless network you are currently connected to with tcpdump, identifies the gateway and network MAC addresses, and reports every client MAC address it observes along with how much data each one sent. Use it to see how many devices share a hotspot and which ones are actually active — it is a passive enumerator; it does not crack, bypass, or modify anything.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### List active users on the current network

See how many devices are on the same hotspot and which of them are actually talking.

```
freewifi
```

Outcome: runs tcpdump on the current wireless interface for 1000 packets and prints each client MAC with its total bytes, sorted by traffic volume (most first), plus the total user count on stderr.

### Target a specific interface and SSID with a bigger sample

Control the capture when several interfaces exist or a longer observation is needed.

```
freewifi -i wlan0 -s CoffeeShop -p 5000 -r 10
```

Outcome: sniffs 5000 packets on wlan0, matching the CoffeeShop SSID to its BSSID, and prints only the top 10 talkers; `-r` limits how many results are shown.
