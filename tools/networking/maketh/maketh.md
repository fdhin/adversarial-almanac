---
name: "maketh"
categories:
  - blackarch-networking
version: "0.2.0"
homepage: "https://packetstormsecurity.com/files/83892/Maketh-Packet-Generator.2.0.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# maketh

> Minimal command-line packet forger for ARP, IP, TCP, UDP and ICMP with full control of the Ethernet header.

## What it's for

Maketh (by Simpp, ~2009) builds and sends individual crafted packets from the Ethernet layer up through ARP, IP, TCP, UDP and ICMP. Every header field can be set explicitly — MAC addresses, opcode, source IP, IP-ID, TTL, IP options (record route, loose/strict source routing), TCP flags and MSS, ICMP type/code with embedded quotations — and source-side fields can be randomized per packet (`--rand-src`, `--rand-ttl`, `--rand-ip-id`). It is a hping-style sender, not a scanner or listener: it transmits to one target (given as a positional argument) and can print the replies it receives. It requires root because it uses raw sockets, and `-h` prints the US help while `-hh` prints the French original.

## Typical use cases

### Send a crafted TCP SYN to a specific port

```
sudo maketh 198.51.100.10 -p tcp -x 1234 -X 80 -S
```

Outcome: transmits a single TCP packet with the SYN flag set from source port 1234 to target port 80, then waits for and prints any reply; add `-c N` to send N packets and `-V` to dump replies in full detail.

### Forge an ARP reply with hand-set L2/L3 addresses

```
sudo maketh 198.51.100.10 -p arp -o 2 -b 198.51.100.1 -B 198.51.100.10 -y 00:11:22:33:44:55 -Y 00:aa:bb:cc:dd:ee
```

Outcome: sends one ARP frame with opcode 2 (reply), claiming sender IP `198.51.100.1` (`-b`) toward `198.51.100.10` (`-B`), using the explicit Ethernet source/destination MACs set by `-y`/`-Y` — the shape used to test how hosts and switches react to unsolicited ARP mappings.

### Emit spoofed-source packets with randomized header fields

```
sudo maketh 198.51.100.10 -p icmp -s ATTACKER_IP -k --rand-ttl --rand-ip-id -c 100
```

Outcome: sends 100 ICMP echo packets whose source IP starts at `ATTACKER_IP` (`-s`) but is then re-randomized for every packet along with TTL and IP-ID (`-k --rand-ttl --rand-ip-id`), producing traffic whose headers vary packet-to-packet; `-W` suppresses reading replies entirely.

*No corpus evidence — derived from upstream documentation.*
