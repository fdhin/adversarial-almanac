---
name: "bittwist"
categories:
  - blackarch-sniffer
version: "2.0"
homepage: "http://bittwist.sourceforge.net/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2014-08-27"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "jamchamb/bittwist"
github-stars: 2
github-pushed: "2015-04-22"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bittwist

> libpcap-based pcap replayer: sends packets from a capture file back onto the wire at a chosen speed, with a companion editor (bittwiste) for the traces.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Bit-Twist complements tcpdump: what tcpdump captures, bittwist sends back out through a network interface — replaying a saved `.pcap` verbatim (original inter-packet timing preserved by default) for IDS/IPS and firewall testing, regression-testing protocol implementations, or regenerating traffic in a lab. `bittwiste` edits trace files: truncate packets at a layer, delete byte ranges, append hex payloads, rewrite the pcap link type, or control checksum recomputation. Replay is L2-faithful: packets go out as captured, so the interface must be able to frame them (an Ethernet capture belongs on an Ethernet interface); it generates traffic rather than acting as a protocol client. Sending requires elevated privileges and care — replayed packets appear on the wire exactly as originally captured.

## Typical use cases

### Replay a capture once onto an interface

```
sudo bittwist -i eth0 -l 1 capture.pcap
```

Outcome: sends every packet from `capture.pcap` out of eth0 keeping the recorded intervals; add `-v` to print each packet's send timestamp (`-vv` also prints hex) as the replay progresses.

### Replay continuously at a capped rate

The upstream recipe for pushing a trace at a fixed line rate — stress-testing an inline sensor.

```
sudo bittwist -i eth0 -l 0 -m 0 -r 100 capture.pcap
```

Outcome: loops the trace until interrupted (`-l 0`), sending back-to-back (`-m 0` suppresses interval scaling) while capping throughput at 100 Mbps (`-r`); `-p <seconds>` instead forces a fixed interval between packets.

### List usable interfaces

```
bittwist -d
```

Outcome: prints every interface libpcap can send on (same enumeration as `tcpdump -D`) with its index and name — use these names for `-i`.

### Edit a trace before replaying it

```
bittwiste -I capture.pcap -O trimmed.pcap -D 15-40
```

Outcome: writes `trimmed.pcap` with bytes 15–40 removed from every packet (`-D` takes an offset or range, first byte = 1); sibling options truncate at a layer (`-L 2|3|4`), append hex payloads (`-X 0302aad1`), rewrite the pcap link type (`-M`), and disable checksum correction (`-C`) — feed the result back to `bittwist -i`.
