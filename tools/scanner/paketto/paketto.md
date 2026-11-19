---
name: "paketto"
categories:
  - blackarch-scanner
version: "3.0"
homepage: "http://www.doxpara.com/paketto/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-21"
build-system: autotools
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/paketto"
github-stars: 0
github-pushed: "2025-12-21"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# paketto

> Dan Kaminsky's advanced TCP/IP toolkit: stateless scanning, layer-2 piping, exotic tracing and DNS sweeps.

## What it's for

Paketto Keiretsu is Dan Kaminsky's 2002 research toolkit from the "Black Ops of TCP/IP" talks. The packaged 3.0 edition is a modernized fork that rebuilds it against current system libraries and prefixes the binaries with `paketto-`. Members: scanrand2 — a stateless TCP port/host/network scanner that separates sending from listening and authenticates replies with an HMAC-SHA1 "Inverse SYN Cookie" embedded in the SEQ field; lc2 (linkcat) — a netcat for layer 2 that streams raw Ethernet in and out with pcap-syntax filters; paratrace2 — a traceroute that rides existing TCP connections via keepalives, so stateful firewalls never see a new session; miname — a large-scale DNS scanner; plus minewt (userspace NAT/MAT gateway), packet2sql, tincan, finx and utilities. Research-era tools: most need root and raw sockets, and output formats include SQL/CSV for large-scale result handling.

## Typical use cases

### High-speed stateless TCP scan

Sweep a network range for open ports with bandwidth limiting.

```
sudo paketto-scanrand2 -b1m 198.51.100.1-254:80,443,22-25
```

Outcome: statelessly SYN-scans the host range on the listed ports (or a preset port set like `quick`, `known`, `all` in place of the port list) capped at the given bandwidth, validates SYN-ACKs against the inverse SYN cookie and prints open hosts/ports. Requires root for raw sockets.

### Split sender and listener across two hosts

Distribute a scan so one host probes and another collects.

```
sudo paketto-scanrand2 -S -i 198.51.100.10 198.51.100.1-254:known
sudo paketto-scanrand2 -L
```

Outcome: the first process only emits requests from the given source IP while `-L` on a second host passively listens and reconstructs results — the classic two-box scanrand mode that keeps the sender off the response path.

### Pipe raw layer-2 traffic like netcat

Bridge two interfaces at the Ethernet layer with filters.

```
sudo paketto-lc2 -l eth0 -p 'tcp port 80' -m eth1
```

Outcome: lc2 reads raw frames matching the filter from the sniff device and re-emits them on the spoof device, providing bidirectional layer-2 streaming (pcap-dump and hex syntaxes both supported) for traffic replay and unusual interception setups.

### Trace a route through a stateful firewall

Discover hops without opening a new connection.

```
paketto-paratrace2 198.51.100.10
```

Outcome: attaches to existing TCP flows with keepalive-derived probes and maps the path from the returned ICMP Time Exceeded messages without sending a single new L4 session — traceroute that survives firewalls blocking classic probes.

### Sweep DNS at scale

Scan large address blocks for a record type.

```
sudo paketto-miname -T PTR -b1m 198.51.1-254.1-254
```

Outcome: miname queries the given record type (A, MX or PTR) across the two range-octet block at the bandwidth cap and reports responding names — the toolkit's DNS census member.

No corpus evidence — derived from the packaged upstream sources (usage banners in scanrand2.c, lc2.c, paratrace2.c and miname.c of the Obsidian-Covenant 3.0 edition, plus the original Paketto README).
