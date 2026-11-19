---
name: "wifi-monitor"
categories:
  - blackarch-sniffer
version: "30.0657e48"
homepage: "https://github.com/DanMcInerney/wifi-monitor"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "DanMcInerney/wifi-monitor"
github-stars: 153
github-pushed: "2021-07-20"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wifi-monitor

> Live ranking of which IPs on your local network are sending the most packets, with 802.11 frame statistics.

## What it's for

wifi-monitor (Dan McInerney) is a passive scapy-based monitor for who is talking on your LAN: it auto-detects the default gateway and interface from the routing table, then counts packets per source IP and prints a continuously updated leaderboard, alongside tallies of 802.11 control and data frames (ack, RTS request, CTS clear, data). With -j it also reports devices newly joining the network, timestamped by their DHCP packets. It is a Python 2 script depending on scapy and nbtscan (aircrack-ng is a declared dependency for putting cards into monitor mode for the 802.11 counters). It identifies and counts — it does not capture payloads or crack anything.

## Typical use cases

### Watch which local IPs talk the most

Run the live per-IP packet ranking.

```
wifi-monitor
```

Outcome: prints a continuously updated table of local IPs ranked by packet count, with colour-coded counts per frame type (ack/req/clear/data); Ctrl-C stops it.

### Track devices joining the network

Also list new devices as they obtain an address.

```
wifi-monitor -j
```

Outcome: in addition to the traffic ranking, each device requesting an IP via DHCP is appended at the bottom with the time it was first seen.

*No corpus evidence — derived from upstream documentation.*
