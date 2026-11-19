---
name: "arpstraw"
categories:
  - blackarch-defensive
version: "27.ab40e13"
homepage: "https://github.com/he2ss/arpstraw"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "he2ss/arpstraw"
github-stars: 26
github-pushed: "2017-06-30"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# arpstraw

> ARP-spoofing (MITM) detection tool that watches a LAN segment and alerts when ARP mappings change.

## What it's for

ARPStraw is a passive Python detector for ARP-spoofing man-in-the-middle attacks: you enumerate the hosts you know and trust — at minimum the default gateway — by IP and MAC in `arpstraw.cfg`, and it watches ARP traffic, live on an interface or replayed from a saved packet capture, notifying you when it detects an ARP-spoof attempt such as a second MAC claiming a trusted host's address. It is detection-only: it warns but does not block or sanitize the cache; pair it with an enforcement daemon such as ArpON when blocking is needed. Live capture requires root.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Declare the known hosts on the segment

Give the detector the ground truth to compare traffic against.

```
cp template.cfg arpstraw.cfg
```

Outcome: creates the configuration file the tool loads at startup; fill in the IP/MAC pairs you trust (gateway first) — detection quality depends directly on how accurate and complete this list is.

### Watch an interface for live attacks

Monitor the segment as it happens.

```
sudo python main.py -i eth0
```

Outcome: sniffs ARP traffic on `eth0` and raises a notification as soon as a spoofing pattern is observed, e.g. a MAC different from the trusted one answering for the gateway's IP; run it on a monitoring host or your own workstation to get immediate warning of an ongoing MITM.

### Analyze a saved capture

Check whether a past incident contained ARP spoofing.

```
sudo python main.py -f capture.pcap
```

Outcome: replays the pcap file through the same detection logic and reports the spoofing events found in it — useful for post-incident review without touching the live network.
