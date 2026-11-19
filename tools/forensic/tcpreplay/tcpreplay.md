---
name: "tcpreplay"
categories:
  - forensics
  - sniffing-spoofing
version: "4.6.1-1"
homepage: "https://tcpreplay.appneta.com"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-09-07"
build-system: autotools
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "appneta/tcpreplay"
github-stars: 1353
github-pushed: "2026-10-26"
github-archived: false
github-issues: 5
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 386
gh-activity-checked: "2026-11-09"
---

# tcpreplay

> Replay saved packet captures onto a live network at controlled speed.

## What it's for

tcpreplay sends the packets of a pcap capture back out through a network interface at a chosen speed — the capture's original timing, a multiple of it, fixed packets per second or Mbps, or as fast as the NIC allows. It is the standard way to test IDS/IPS, firewalls, and NetFlow exporters by replaying known traffic at them, and ships as a suite alongside tcpprep (client/server traffic classification), tcprewrite (header rewriting), and tcpliveplay (stateful TCP replay). It injects at Layer 2 without establishing connections, so packets leave with whatever MACs, IPs, and ports the capture contains — never replay onto a production network.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Replay a capture at its original timing

Put a recorded conversation back on the wire.

```
sudo tcpreplay -i eth0 sample.pcap
```

Outcome: injects every packet from sample.pcap onto eth0 reproducing the capture's inter-packet timing, then prints a report such as `Actual: 1000 packets (486400 bytes) sent in 9.89 seconds` with the achieved rate.

### Control the replay rate

Test a sensor under known load.

```
sudo tcpreplay -i eth0 -t sample.pcap
sudo tcpreplay -i eth0 --pps 100 sample.pcap
sudo tcpreplay -i eth0 -M 10 sample.pcap
```

Outcome: `-t` (`--topspeed`) sends as fast as possible, `--pps 100` at 100 packets per second, `-M 10` at 10 Mbps, and `-x 2` at twice the original speed — the speed options are mutually exclusive, pick one.

### Loop a capture continuously

Keep traffic flowing for a soak test.

```
sudo tcpreplay -i eth0 --loop 10 --loopdelay-ms 1000 sample.pcap
```

Outcome: replays the file ten times, pausing one second between iterations (the loop-delay flags require `--loop`); `--stats 5` additionally prints throughput statistics every five seconds.

### Send only part of a capture

Replay a slice instead of the whole file.

```
sudo tcpreplay -i eth0 --include 1-100 sample.pcap
```

Outcome: sends only packets 1 through 100; `--exclude 50-60` skips a range instead, and `--limit 500` stops the replay after 500 packets.
