---
name: "zizzania"
categories:
  - blackarch-wireless
version: "124.8f2062f"
homepage: "https://github.com/cyrus-and/zizzania"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-28"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "cyrus-and/zizzania"
github-stars: 318
github-pushed: "2023-12-09"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# zizzania

> Captures crackable WPA handshakes using targeted, rate-limited deauthentication instead of blind flooding (Andrea Cardaci).

## What it's for

zizzania sniffs wireless traffic for WPA handshakes and writes out only the frames needed for offline decryption — one beacon plus the EAPOL frames plus data — rather than a full mixed capture. To speed up handshake collection it sends 802.11 deauthentication frames to the stations whose handshake is still missing, but unlike a blanket deauth flood it handles retransmissions and reassociations and deliberately limits how many deauths each station receives. It can run passively (no deauths at all) or work entirely offline on existing pcap files, filtering which BSSIDs and stations to include or exclude by address/mask. It is the tool to reach for when you want a handshake capture with minimal disruption to the network; it does not itself crack the handshake — hand the output pcap to aircrack-ng or hashcat.

## Typical use cases

### Target one AP and capture its clients' handshakes

Put the interface in monitor mode on a channel, deauth only the stations of one AP, and save a crack-ready capture.

```
sudo zizzania -i wlan0 -c 6 -b AA:BB:CC:DD:EE:FF -S 00:11:22:33:44:55/ff:ff:ff:00:00:00 -w out.pcap
```

Outcome: tunes `wlan0` to channel 6, considers only BSSID `AA:BB:CC:DD:EE:FF` (`-b`, address/mask allowed) while excluding stations whose address starts with `00:11:22` (`-S`), sends measured deauths to the remaining stations, and appends one beacon plus the EAPOL frames plus data for each handshake obtained to `out.pcap`; `-q` makes it exit after the first complete handshake.

### Collect handshakes passively

Disturb nothing and simply wait for handshakes to happen on their own.

```
sudo zizzania -i wlan0 -n
```

Outcome: monitors the current channel in passive mode (`-n`, no deauthentication frames sent) and records any WPA handshake traffic that occurs naturally; `-M` skips the tool's own RFMON setup when the interface was already prepared externally (e.g. by airmon-ng).

### Clean up an existing capture offline

Strip a full capture down to the frames an offline cracker actually needs.

```
zizzania -r in.pcap -S 00:11:22:33:44:55 -2 -w out.pcap
```

Outcome: reads `in.pcap` without any interface, drops the excluded station's traffic, treats a handshake as complete after only the first two EAPOL messages (`-2`; `-3` for three — enough for unicast decryption) and writes the trimmed capture to `out.pcap`, typically far smaller than the input.

*No corpus evidence — derived from upstream documentation.*
