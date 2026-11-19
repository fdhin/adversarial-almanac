---
name: "pcapteller"
categories:
  - blackarch-sniffer
version: "1.1"
homepage: "https://www.encripto.no/nb/downloads/tools/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "BlackArch/pcapteller"
github-stars: 9
github-pushed: "2019-08-23"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pcapteller

> Replay a recorded pcap into your own network, rewriting MAC and IP addresses to fit it.

## What it's for

You hold a capture of an attack or malware infection recorded in another network and want your own sensors to see that scenario as if it unfolded locally — to tune blue-team detection or to seed red-team decoy traffic. Pcapteller (Juan J. Guelfo at Encripto AS) reads a libpcap file and re-sends every packet out an interface of your choice; before replay it can rewrite layer-2/3 addresses, mapping a comma-separated list of MAC/IP addresses as they appear in the file (`-a`/`-c`) onto replacement addresses on the wire (`-b`/`-d`), paired positionally. With `-r` it honors the original inter-arrival delays so pacing-sensitive detections see the original timing. It is a 2016 Python 2/Scapy tool shipped as-is: only MAC/IP rewriting is supported, and DNS responses are documented upstream to replay malformed.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Replay a capture at full speed

Get the recorded packets onto your wire.

```
sudo pcapteller -f scenario.pcap -i eth0
```

Outcome: the tool prints the packet count found in the file, replays them all out eth0 as fast as Scapy can send, and reports "Replay complete" — watch them arrive on the sensor you are tuning.

### Preserve the original timing

Keep inter-arrival delays so timing-based analysis sees the real scenario.

```
sudo pcapteller -f scenario.pcap -i eth0 -r
```

Outcome: replay speed is governed by the pcap's own timestamps ("Real time enabled" is announced) instead of full speed; the replay takes as long as the original capture and bursts stay bursts.

### Rewrite addresses to the local address space

Make foreign traffic look like it belongs to your network.

```
sudo pcapteller -f scenario.pcap -i eth0 -c 198.51.100.10 -d 203.0.113.20
```

Outcome: every packet in the capture carrying IP 198.51.100.10 is re-sent with 203.0.113.20 in its place; MAC addresses work the same way with `-a 00:11:22:33:44:55 -b 00:de:ad:be:ef:00`, and comma-separated lists rewrite several addresses in one pass, paired in order.
