---
name: "mausezahn"
categories:
  - blackarch-dos
version: "0.40"
homepage: "http://www.perihel.at/sec/mz/"
packaged-by: [blackarch]
blackarch-builddate: "2015-01-29"
build-system: cmake
license: GPL
meta-checked: "2026-11-09"
github-repo: "uweber/mausezahn"
github-stars: 57
github-pushed: "2023-03-22"
github-archived: true
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mausezahn

> Command-line packet crafter and traffic generator that sends nearly any frame, valid or deliberately malformed.

## What it's for

Mausezahn (binary `mz`) builds and transmits individual packets or sustained packet streams at layer 2/3: arbitrary raw bytes on the wire, or structured packets via built-in per-protocol builders (DNS, BPDU/STP, RTP, ARP, ICMP, TCP, and more). Typical purposes are stressing multicast/VoIP networks, measuring RTP jitter between two hosts, auditing firewall and IDS reactions to hostile or malformed traffic, and reproducible DoS-style tests in a lab. It needs root and works on Linux only. Note that upstream marks this standalone 0.40 repository deprecated — the code was merged into the netsniff-ng toolkit, where mausezahn ships as one of its experimental utilities (see [[netsniff-ng]] for the kit; its `trafgen` covers low-level multithreaded generation and is a separate tool). This note covers the standalone packaged version, whose `mz` interface is unchanged in the kit.

## Typical use cases

### Send an arbitrary raw frame repeatedly

Transmit a hand-crafted byte sequence as an Ethernet frame a fixed number of times.

```
mz eth0 -c 1000 "ff:ff:ff:ff:ff:ff ff:ff:ff:ff:ff:ff cc:dd 00:00:00:ca:fe:ba:be"
```

Outcome: 1000 copies of the frame go out on eth0 — intentionally invalid (a too-short runt with a broadcast source address), which is exactly the point when testing how a switch or NIC handles malformed input. `-c 0` sends endlessly until interrupted.

### Forge a DNS response

Impersonate a name server by injecting a spoofed reply toward a host.

```
mz eth0 -A 198.51.100.20 -B 198.51.100.10 -t dns "q=example.com, a=198.51.100.99"
```

Outcome: a DNS response is sent claiming example.com resolves to 198.51.100.99, with source address 198.51.100.20 (the "server") and destination 198.51.100.10. Run `mz -t dns help` to list every manipulable DNS field.

### SYN-flood a subnet's well-known ports

Stress a firewall or host with endless half-open connections across a port range.

```
mz eth0 -c 0 -A rand -B 198.51.100.0/25 -t tcp "flags=syn, dp=1-1023"
```

Outcome: unlimited (`-c 0`) SYN packets with randomized source addresses (`-A rand`) hit ports 1-1023 on every host in 198.51.100.0/25, letting you observe SYN-flood handling (drops, SYN cookies, alerts) in a controlled test.

### Claim the spanning-tree root role

Generate BPDUs that announce your machine as the root bridge.

```
mz eth0 -c 0 -d 2s -t bpdu vlan=314
```

Outcome: root-bridge BPDUs for VLAN 314 are emitted every two seconds; a switch that accepts them recalculates spanning tree around you — a classic layer-2 audit check. Per-VLAN parameters are all configurable; `mz -t bpdu help` lists them.

### Measure RTP jitter between two hosts

Run a sender on one machine and a receiving analyzer on another to quantify delay variation on a VoIP path.

```
TX: mz eth0 -t rtp -B target.example.com
RX: mz eth0 -T rtp "log, path=/tmp"
```

Outcome: the sender emits RTP packets every 30 ms; the receiver writes moving-average jitter data as a comma-separated, timestamped file under /tmp ready for plotting in R, Octave, or matplotlib.

### List every packet builder

Discover which protocols have dedicated builders before crafting.

```
mz -t help
```

Outcome: prints all supported packet types; appending `help` to any type (e.g. `mz -t dns help`) prints that builder's tunable fields.

*No corpus evidence — derived from upstream documentation.*
