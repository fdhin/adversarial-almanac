---
name: "rtpbreak"
categories:
  - blackarch-networking
version: "1.3a"
homepage: "https://packetstorm.news/files/id/66494/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-09-30"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "relops/rtpbreak"
github-stars: 7
github-pushed: "2017-12-28"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rtpbreak

> Detects, reconstructs and dumps RTP streams from live traffic or pcaps without needing the signaling.

## What it's for

rtpbreak sniffs a network interface (or reads a pcap file) and identifies RTP sessions purely from RTP's statistical patterns — no SIP/H.323/SCCP signaling required. For every detected stream it writes the reordered packets (`.pcap`), the raw payload (`.raw`, directly decodable by sox for audio codecs) and a per-stream log listing peers, SSRC, payload type, packet and loss counts. It can filter on payload type, payload length and ports, fill gaps left by lost packets so decoders stay in sync, and pair the two directions of a call. It is a passive analysis tool — it never sends packets.

## Typical use cases

### Record a live VoIP call

Capture both directions of a conversation as decodable audio.

```
sudo rtpbreak -i eth0 -m -g -d logz
```

Outcome: sniffs eth0 in promiscuous mode, announces each stream as it is detected (e.g. `[rtp0] detected: pt=0(g711U) 198.51.100.10:2072 => 198.51.100.20:2074`), pairs the reverse stream automatically, and writes rtp.0.N.raw/.pcap/.txt into logz/; gap filling (`-g`) keeps the raw dumps decodable, so `sox -r8000 -c1 -t ul logz/rtp.0.0.raw -t wav call.wav` yields playable audio for G.711 streams. A SIGUSR2 signal prints live per-session statistics.

### Reconstruct RTP sessions from a capture file

Analyze a pcap offline instead of listening live.

```
rtpbreak -r capture.pcap -d logz -P2 -t100 -T100
```

Outcome: reads capture.pcap with a 2-packet detection pattern, 100 s session timeout and 100 s pattern timeout, producing the same per-session raw/pcap/log set in logz/ without touching the network.

### Isolate one codec

Cut through unrelated UDP noise by RTP payload type.

```
sudo rtpbreak -i eth0 -m -y 0 -d logz
```

Outcome: only streams whose RTP payload type is exactly 0 (ITU-T G.711 µ-law) are captured; run `rtpbreak -k` first to dump the list of known payload-type numbers and their codecs before choosing a value for `-y`.

*No corpus evidence — derived from upstream documentation.*
