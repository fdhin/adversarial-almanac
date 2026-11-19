---
name: "synner"
categories:
  - blackarch-spoof
version: "1.1"
homepage: "http://packetstormsecurity.com/files/69802/synner.c.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# synner

> Hand-rolled Ethernet/IP/TCP packet spoofer — every header field under your control, with random source identities and a continuous-fire mode for firewall and DoS testing.

## What it's for

A raw PF_PACKET generator (C, by sm4x) that composes eth->ip->tcp frames from scratch: spoofed source IP and MAC (or randomly generated sources), source and destination ports, TCP flag combinations (syn/ack/push/urgent/reset/fin/all), window size and payload padding. Its stated purpose is testing firewalls and denial-of-service behavior — e.g. driving SYN floods with random source IPs against a target port, or firing single crafted segments to see how a filter reacts. Single-shot by default; `-x` switches to a continuous loop with an optional packet count. Root required. Note the flag style: values attach directly to the letter (`-ieth0`, `-p80`).

## Typical use cases

### Fire one fully-spoofed crafted segment

```bash
sudo synner -ieth0 -s198.51.100.10 -S00:AA:BB:CC:00:00 -d198.51.100.20 -D00:DD:EE:AA:DD:BB -p 6984 -u 5555 -l 512 -fas
```

Outcome: one Ethernet/IP/TCP frame leaves `eth0` claiming to come from 198.51.100.10 (`-s` IP, `-S` MAC) toward 198.51.100.20 (`-d`, `-D`), destination port 6984, source port 5555, 512 bytes of zero padding (`-l`), SYN+ACK flags (`-fas`). Capture on the target to see exactly what survives the path.

### SYN-flood a target with random source IPs

```bash
sudo synner -ieth0 -sr -S00:DD:EE:AA:DD:FF -d198.51.100.20 -D00:DD:EE:AA:DD:BB -p80 -u500 -fx -x0
```

Outcome: `-sr` randomizes the source IP per packet, `-fx` sets all flags, and `-x0` loops forever (`-x10000` would cap it at 10 000 packets), so a continuous stream of spoofed-origin segments hits port 80 — the load/firewall-reaction pattern it was built to exercise.

*No corpus evidence — derived from upstream documentation.*
