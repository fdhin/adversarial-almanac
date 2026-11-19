---
name: "synflood"
categories:
  - blackarch-dos
version: "0.1"
homepage: "http://thesprawl.org/projects/syn-flooder/"
packaged-by: [blackarch]
blackarch-builddate: "2018-01-22"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# synflood

> Minimal Perl SYN-flood generator (iphelix, thesprawl.org) for demonstrating TCP half-open connection exhaustion.

## What it's for

A ~15-line Net::RawIP script that hammers one host and port with TCP SYN
packets from randomized source ports, in an endless loop. It exists to
illustrate and reproduce the classic SYN-flood resource-exhaustion effect —
useful in a lab to show a target's backlog filling with half-open
connections and to exercise mitigations such as SYN cookies or SYN proxying.
It has no rate control, no reporting, and no built-in stop: terminate it
with Ctrl+C. Requires root (raw sockets) and the Net::RawIP Perl module.
It is a demonstration aid, not a measurement or load-testing framework —
for multi-protocol stress injection see [[t50]].

## Typical use cases

### Flood a lab host with TCP SYN packets

Run against your own test server to fill its half-open connection backlog.

```
sudo synflood 198.51.100.10 198.51.100.25 80
```

Outcome: sends SYN packets to 198.51.100.25:80 in an infinite loop, each
from a random source port with the claimed source address 198.51.100.10.
On the target, `ss -tan state syn-recv | wc -l` climbs while the flood runs
and new legitimate handshakes stall; `tcpdump -i eth0 tcp port 80` shows the
inbound SYNs with no SYN-ACK completion. Stop with Ctrl+C.

### Check the argument contract

Run with no arguments to confirm the positional interface before scripting
it into a lab exercise.

```
synflood
```

Outcome: prints the usage line `./synflooder source_ip destination_ip
destination_port` — the three positional arguments are spoofed source IP,
destination IP, destination port.

*No corpus evidence — derived from upstream documentation.*
