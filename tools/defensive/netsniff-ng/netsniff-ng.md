---
name: "netsniff-ng"
categories:
  - respond
  - sniffing-spoofing
version: "0.6.9-2"
homepage: "http://netsniff-ng.org/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-03-29"
build-system: cmake
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "netsniff-ng/netsniff-ng"
github-stars: 1429
github-pushed: "2025-06-11"
github-archived: false
github-issues: 57
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netsniff-ng

> Zero-copy Linux networking toolkit: high-speed capture, replay, and traffic generation.

## What it's for

A fixed set of eight utilities built around zero-copy packet I/O, so packets move between kernel and user space without being copied: `netsniff-ng` (pcap capture/replay/reformatting analyzer), `trafgen` (multithreaded low-level packet generator), `mausezahn` (high-level packet crafter with per-protocol builders), `ifpps` (top-like interface statistics), `flowtop` (top for netfilter connection tracking), `astraceroute` (AS-path traceroute), `bpfc` (BPF compiler/JIT disassembler), and the experimental `curvetun` (curve25519 IP tunnel). Reach for it when capture/replay speed or wire-rate packet crafting on Linux matters. It is kernel-plumbing oriented — not an application-protocol dissector like Wireshark — and mausezahn/curvetun are marked experimental upstream.

## Typical use cases

### Capture traffic to a pcap, optionally filtered

Capture a link with a BPF expression and write a pcap without per-packet console noise.

```
netsniff-ng -i eth0 -o capture.pcap -s "tcp port 443"
```

Outcome: Matching packets are written to capture.pcap through a zero-copy ring buffer; drop `-s` to also print a live hex/ASCII dump per packet.

### Inspect an existing pcap offline

Read a saved capture without loading a GUI.

```
netsniff-ng -i capture.pcap -o -
```

Outcome: Each packet in the file is printed to stdout in netsniff-ng's hex+ASCII format for quick offline inspection.

### Replay a pcap onto the wire

Reinject recorded traffic against a lab device.

```
netsniff-ng --in capture.pcap --mmap --out eth0
```

Outcome: The recorded packets are retransmitted onto eth0 using mmap'd I/O; add `--silent` to suppress per-packet output during replay.

### Generate traffic from a packet definition

Send crafted packets at defined rates for load or protocol testing.

```
trafgen --dev eth0 --conf trafgen.cfg --num 1000
```

Outcome: Exactly 1000 packets built from the definitions in trafgen.cfg are transmitted on eth0; use `--num 0` for continuous transmission and `--gap 1000us` to pace them.

### Craft protocol-specific packets

Build precise probes with a high-level packet builder instead of raw hex.

```
mausezahn eth0 -c 10 -t tcp "dp=443, flags=syn" -B target.example.com
```

Outcome: 10 TCP SYN packets to port 443 on target.example.com are sent via raw sockets; `mausezahn -t tcp help` lists every field the TCP builder accepts (udp, dns, icmp, arp, bpdu, syslog and others have their own builders).

### Watch interface statistics live

Monitor a busy interface with kernel-level counters.

```
ifpps -pd eth0
```

Outcome: A top-style view of eth0 (throughput, packet rates, drops, per-CPU network statistics) refreshes until interrupted.

### List live connection-tracking entries

See what connections the kernel is currently tracking.

```
flowtop -46 -U -s
```

Outcome: A top-like listing of netfilter conntrack entries for IPv4 and IPv6, including UDP flows with source endpoints shown; requires root to attach to the conntrack subsystem.

### Trace the autonomous-system path to a host

Find which AS hops — and potentially filtering middleboxes — sit between you and a target.

```
astraceroute -i eth0 -N -H example.com
```

Outcome: Prints each TTL hop with its autonomous system; `-G` (--nofrag) additionally probes for path MTU/DPI behavior along the way.

### Compile a filter to BPF instructions

Turn a tcpdump-syntax filter into reusable compiled BPF.

```
bpfc -f tcpdump -i http.bpf
```

Outcome: The filter source in http.bpf is compiled and emitted in tcpdump-style format; `-f xt_bpf` instead emits bytecode suitable for `iptables -m bpf --bytecode`.

*No corpus evidence — derived from upstream documentation.*
