---
name: "screamer"
categories:
  - blackarch-recon
version: "0.2.0"
homepage: "https://github.com/ifritnoises/screamer"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-06-10"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "ifritnoises/screamer"
github-stars: 42
github-pushed: "2026-06-13"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
---

# screamer

> Subnet and gateway discovery for networks with unknown addressing — TTL-trick topology mapping plus passive traffic sniffing.

## What it's for

Screamer answers "what does this network look like?" when nobody will give you an address plan. Instead of port-scanning a huge range, active mode probes only the addresses most likely to be gateways (by default .1 and .254 in each /24) with incrementally increasing TTL values, so each router along the path reveals itself with ICMP Time Exceeded replies — producing a hop/subnet map at a fraction of the cost of a full scan. It cycles protocols (ICMP echo, ICMP timestamp, TCP, UDP) to get past filters that drop one traffic type. Passive mode sniffs an interface (or parses a pcap) for ARP, NDP, SSDP, CDP, LLDP, DHCP, DHCPv6, LLMNR, NBT-NS and mDNS to inventory nearby hosts without sending anything. Python 3.7+, raw sockets, runs as root.

## Typical use cases

### Map an unknown network actively

```
sudo screamer active 198.51.100.0/24 --out-dot topology.dot --out-subnets subnets.txt
```

Outcome: probes likely gateway addresses with rising TTLs, then writes the discovered hop topology to a Graphviz DOT file and the reachable subnet list to a text file.

### Get past ICMP filters with another protocol

```
sudo screamer active 198.51.100.0/24 -m icmp-timestamp
sudo screamer active 198.51.100.0/24 -m udp --dport 161
```

Outcome: repeats the TTL trace using timestamp requests, or UDP probes to a closed port whose ICMP port-unreachable reply still proves a live host — for networks that drop plain ping.

### Inventory a broadcast domain passively

```
sudo screamer passive --iface eth0 --timeout 300 --output matched.pcap
```

Outcome: captures and decodes discovery traffic for five minutes, printing the devices heard on the segment and saving the matched packets to a pcap.

### Parse an existing capture without root

```
screamer passive --pcap capture.pcap
```

Outcome: extracts the same host/protocol inventory from a saved capture file — no interface or privileges required.

*No corpus evidence — derived from upstream documentation (flags verified against the packaged argparse).*
