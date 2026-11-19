---
name: "rootwire"
categories:
  - sniffer
version: "git (2026-11-07)"
homepage: "https://github.com/EONRaider/RootWire"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "EONRaider/RootWire"
github-stars: 792
github-pushed: "2026-11-07"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 128
gh-activity-checked: "2026-11-09"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: AGPL3
github-issues: 0
---

# rootwire

> GNU/Linux traffic monitor: live capture, pcap replay and NDJSON output with deep per-layer protocol decoding.

## What it's for

RootWire (eonraider's successor to Packet-Sniffer; CLI `rootwire`, PyPI package `rootwire`) watches one or more interfaces with a raw AF_PACKET socket — frames timestamped by the kernel itself — and decodes each one layer by layer: Ethernet/VLAN/LLC/LLDP/CDP/STP/MPLS, IPv4/IPv6 with extension headers, tunnels (GRE, IP-in-IP, VXLAN, IPsec ESP/AH), TCP/UDP/SCTP, routing protocols (OSPF, RIP, BGP, PIM, VRRP/HSRP) up to DNS, DHCP, TLS, NTP, TFTP, RADIUS and STUN, with HTTP available on request. Each frame renders as an indented human-readable tree, is written to nanosecond-precision pcap, or streams out as one JSON object per frame; checksums are recomputed and mismatches flagged inline. It is a pure monitor — it explains traffic, it does not inject or modify anything. Live capture needs root or CAP_NET_RAW; replaying a file with `-r` needs no privileges. Python 3.12+, GNU/Linux only.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Capture live traffic and keep the evidence

Watch an interface and save everything it sees.

```
sudo rootwire -i eth0 -w session.pcap
```

Outcome: frames render live as `[>]` frame headers with `[+]` decoded layers beneath; every frame is simultaneously written to `session.pcap`, and on Ctrl-C a `[=]` summary on stderr reports frame/byte counts, a per-protocol tally and the largest conversations.

### Filter in the kernel

Drop the noise before it reaches userspace.

```
sudo rootwire -i eth0 --filter "tcp and port 80"
```

Outcome: only frames matching the filter expression reach the decoder — canned names (`arp`, `ip6`, `tcp`, `udp`) or full expressions with `host`/`port`, `src`/`dst` prefixes, `and`/`or`/`not` and parentheses.

### Merge several interfaces into one stream

Watch a machine's whole network edge at once.

```
sudo rootwire -i eth0 -i wlan0
```

Outcome: both interfaces are captured concurrently and interleaved into one stream, each frame tagged with the interface it arrived on.

### Replay and mine a capture offline

No privileges needed, machine-readable output.

```
rootwire -r session.pcap --json | jq -c 'select(.error) | {number, error}'
```

Outcome: one NDJSON object per frame on stdout (banner, warnings and the summary go to stderr, so the pipe stays clean); this example lists every frame whose decode stopped on a malformed header and why — the JSON records carry timestamps, interface, lengths and every decoded layer.

### Decode a protocol on a nonstandard port

Follow traffic where the port doesn't match the protocol.

```
rootwire -r lab.pcap --decode-as tcp.port=8080:HTTP
```

Outcome: segments on TCP port 8080 are decoded as HTTP (HTTP is never decoded unasked); the same flag follows a TFTP transfer past its request (`udp.port=40000:TFTP`) or disables a decoder outright with `none` (e.g. `udp.port=1812:none`), repeatable per capture.
