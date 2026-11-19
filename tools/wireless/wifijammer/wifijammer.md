---
name: "wifijammer"
categories:
  - blackarch-wireless
version: "100.be05dab"
homepage: "https://github.com/DanMcInerney/wifijammer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "DanMcInerney/wifijammer"
github-stars: 4322
github-pushed: "2024-07-20"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wifijammer

> Scapy script that continuously deauthenticates every Wi-Fi client and access point in range.

## What it's for

wifijammer, by Dan McInerney, is a Python 2/scapy script that jams 802.11 traffic by replaying deauthentication frames. With no arguments it finds or reuses a monitor interface, hops channels mapping APs and clients, then continuously sends deauths in both directions (client→AP, AP→client, and to the AP's broadcast address) against everything it saw. The options give granularity: lock to one channel or AP, cap the packet count, pace the sends to avoid scapy's "no buffer space" error, skip MACs (e.g. your own rogue AP), and extend the band to channels 12–13 outside North America. Effectiveness is bounded by your wireless card — injection support is required, and better cards mean wider jamming radius. Deauthentication is a disruption attack only; it recovers no keys.

## Typical use cases

### Jam everything within range

Let it find the strongest interface and hit all APs and clients it can see.

```
sudo wifijammer
```

Outcome: enables monitor mode on the best interface, spends the first pass (1 s per channel, 1–11) identifying APs and clients, then continuously sends deauth packets to every target discovered while still adding new ones as they appear; runs until stopped.

### Deauthenticate one AP's clients

Lock onto a single access point instead of the whole airspace.

```
sudo wifijammer -a AA:BB:CC:DD:EE:FF -c 2
```

Outcome: skips channel hopping and stays on channel 2 (the target's channel), deauthenticating every device that communicates with the given MAC; pass an AP's MAC to hit all its clients, or a client's MAC to single out that device and its peers.

### Tune the attack and spare your own gear

Control packet count, pacing, targets and channel band explicitly.

```
sudo wifijammer -c 1 -p 5 -t .00001 -s AA:BB:CC:DD:EE:FF -d --world
```

Outcome: stays on channel 1 and sends 5 deauths per direction per target with a .00001 s interval (the fix for scapy "no buffer space" errors), never deauths the skipped MAC (handy for keeping your own rogue AP clean), omits deauths to AP broadcast addresses for speed, and scans up to channel 13 for non-North-American bands.

### Keep jamming while moving

Refresh the target list as signal strengths change on the move.

```
sudo wifijammer -m 10
```

Outcome: caps the deauth list at 10 client/AP combos; once the cap is hit the list clears and repopulates from live traffic, so the strongest nearby signals stay targeted — add `-n` to stop the list clearing at the cap instead.

*No corpus evidence — derived from upstream documentation (repo README, usage and options).*
