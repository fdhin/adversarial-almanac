---
name: "packit"
categories:
  - blackarch-networking
version: "2.0"
homepage: "http://packetfactory.openwall.net/projects/packit/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-12-20"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/packit"
github-stars: 0
github-pushed: "2025-12-20"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# packit

> libnet/libpcap packet generator and sniffer for crafting, injecting, and capturing arbitrary IPv4 traffic.

## What it's for

packit is a network auditing tool built on libnet and libpcap that combines a tcpdump-style capture mode with fully controllable packet injection: TCP, UDP, ICMP and raw IP headers can be set field-by-field (addresses, ports, flags, sequence numbers, TTL, TOS), with ARP and Ethernet-level options for link-layer testing. A trace mode sends incrementing-TTL probes for traceroute-style path discovery, and capture mode supports BPF filter expressions, snaplen, and pcap file output. Reach for it when testing firewall rule ordering, IDS signatures, or stack behavior against precisely shaped packets. The packaged build is the modernized edition of the original Packet Factory tool, refactored to compile against current gcc/libnet; it does not reassemble streams or do passive traffic analysis.

## Typical use cases

### Sniff an interface with a BPF filter

Capture live traffic and inspect payloads on the spot.

```
packit -m capture -i eth0 -c 50 -s 0 -X 'tcp port 443'
```

Outcome: prints up to 50 matching packets to the console with full-length snaplen and hex/ASCII payload dumps (`-X`), then exits — a tcpdump-like view without writing files.

### Save matching traffic to a pcap file

Keep a filtered capture for later analysis.

```
packit -m capture -i eth0 -w session.pcap 'host 198.51.100.10'
```

Outcome: writes every packet matching the BPF expression to `session.pcap`, openable in Wireshark or replayable by other tools.

### Inject a crafted TCP SYN burst

Generate precisely shaped packets toward a listener.

```
packit -m inject -t TCP -s 198.51.100.10 -d 198.51.100.20 -S 51000 -D 80 -F S -b 5
```

Outcome: sends 5 SYN-only packets (`-F S`) per interval from source address/port `-s`/`-S` to destination address/port `-d`/`-D`; confirm arrival with a sniffer on the target — useful for firewall and IDS rule testing.

### Trace a path with ICMP probes

Discover the routing path to a host using packit's traceroute mode.

```
packit -m trace -d 198.51.100.20
```

Outcome: sends ICMP probes with incrementing TTL toward the destination and prints the hop addresses that reply, traceroute-style.

*No corpus evidence — derived from upstream documentation.*
