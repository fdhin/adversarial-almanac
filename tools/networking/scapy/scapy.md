---
name: "scapy"
categories:
  - networking
homepage: "https://github.com/secdev/scapy"
origin: "hacktricks"
recipe: "upstream"
packaged-by: [blackarch, arch]
build-system: python
license: GPL-2.0
meta-checked: "2026-11-13"
github-repo: "secdev/scapy"
github-match: "exact"
github-stars: 12689
gh-checked: "2026-11-13"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# scapy

> Python packet-crafting toolkit: forge, send, sniff, match and replay any protocol stack from an interactive shell or a library.

## What it's for

Scapy is the canonical packet manipulation program: it builds packets layer
by layer (`IP()/TCP()/...`), sends them, captures answers, and dissects
replies with full protocol-field access. Because you compose arbitrary
layer stacks, it does the things fixed-purpose tools cannot — invalid
frames, custom 802.11 injections, combined techniques (VLAN hopping + ARP
poisoning), protocol fuzzing — and it can replace hping, arpspoof, arping
or tcpdump for ad-hoc jobs. It works both as an interactive shell (`scapy`)
and as a Python library (`from scapy.all import *`), reads and writes pcap
files, and runs on Linux, macOS, \*BSD and Windows. It is a building block,
not a report generator: you get packets and answers, and interpret them
yourself.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Ping a host from the interactive shell

Send one ICMP echo request and inspect the reply.

```
sudo scapy
>>> p = IP(dst="target.example.com")/ICMP()
>>> r = sr1(p)
>>> r[IP].src
```

Outcome: sends a single crafted ICMP packet, waits for the first answer
(`sr1`), and prints the replying source IP — the minimal proof of
reachability and the standard way to prototype any packet exchange.

### SYN-scan a set of ports

Probe many ports (or hosts) in one call.

```
>>> ans, unans = sr(IP(dst="198.51.100.10")/TCP(dport=[21,22,23,80,443], flags="S"))
>>> ans.summary()
```

Outcome: sends one SYN per target port and collects answered/unanswered
pairs; answered SYN-ACKs mark open ports, RSTs mark closed ones. Lists of
hosts (`dst=[...]`) and port ranges (`dport=(440,443)`) work the same way,
with `inter`, `retry` and `timeout` to pace the scan.

### Sniff traffic with a BPF filter

Capture live packets and act on each.

```
>>> pkts = sniff(filter="tcp port 80", count=10, prn=lambda x: x.summary())
```

Outcome: captures 10 matching packets, printing a one-line summary of each
as it arrives (`prn` is your callback — use `x.show()` for full dissection);
the returned packet list can be indexed field by field (`pkts[0][IP].src`).

### Save and replay a capture

Round-trip packets through pcap files.

```
>>> wrpcap("capture.cap", pkts)
>>> pkts2 = sniff(offline="capture.cap")
```

Outcome: writes captured/forged packets to a pcap readable by tcpdump and
Wireshark, and reads any pcap back into Scapy packet objects for
dissection or re-send — `sendp(rdpcap("capture.cap"))` replays a capture on
the wire.

### Craft a DNS query by hand

Ask a resolver directly with a forged packet.

```
>>> r = sr1(IP(dst="198.51.100.10")/UDP()/DNS(rd=1, qd=DNSQR(qname="target.example.com")))
>>> r.an.rdata
```

Outcome: sends a raw UDP DNS query (recursion desired) and exposes the
answer resource record — useful for testing resolvers, spoofing sources,
or querying record types a client tool will not let you touch.

### TCP traceroute

Map the path with SYN packets.

```
>>> ans, unans = sr(IP(dst="target.example.com", ttl=(4,25), id=RandShort())/TCP(flags=0x2))
>>> ans.make_table(lambda s, r: (s.ttl, r.src, r.sprintf("%TCP.flags%")))
```

Outcome: sends SYNs with increasing TTLs and tabulates TTL → replying hop
→ TCP flags, revealing filtered paths where ICMP traceroute gets no
answers; the built-in `traceroute(["target.example.com"])` wraps the same
flow and can draw the route graph.

### Fuzz a network service

Throw structurally random packets at a listener.

```
>>> send(IP(dst="target.example.com")/fuzz(UDP()/NTP(version=4)), loop=1)
```

Outcome: continuously sends packets whose default fields are kept valid but
whose other fields are randomized (`fuzz()`), letting you watch a service's
robustness without hand-designing malformed inputs; `sendp(..., iface=...)`
does the same at layer 2.
