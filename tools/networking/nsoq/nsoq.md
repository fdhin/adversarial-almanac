---
name: "nsoq"
categories:
  - blackarch-networking
version: "1.9.5"
homepage: "http://www.nsoq.org/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2015-02-08"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "FelipeEcker/nsoq"
github-stars: 3
github-pushed: "2016-02-29"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nsoq

> Packet crafting and testing tool covering ICMP, TCP, UDP and ARP/RARP with full control of L2-L4 header fields.

## What it's for

Nsoq (by Felipe Ecker) builds and sends arbitrary network packets: many ICMP types, TCP with any flag combination, UDP, and link-layer ARP/RARP — with control over source/destination IP and MAC, ports, packet size, TTL, threads and payload. It is aimed at analyzing and testing TCP/IP scenarios: protocol behavior checks, header spoofing, ARP-based host discovery and tcpdump-style listening on every protocol it speaks. It also ships attack modes (ARP poisoning, MAC flooding, web/DoS stress testing) and an IRC-remote-control mode, and requires root to run. Reach for it when one command line must control both the Ethernet (MAC) and IP/transport layers of the packets you send.

## Typical use cases

### Ping a host with an ICMP echo request

Send a single crafted ICMP packet.

```
nsoq -d target.example.com -Ie
```

Outcome: one ICMP Echo Request goes to `target.example.com` and the tool waits for and displays the Echo Reply; add `-z` to fire without waiting, and `-x 512 -t 50` to set 512-byte packets with TTL 50.

### Probe a TCP port with a SYN packet

Test reachability with a raw SYN.

```
nsoq -d target.example.com -Ts -p 6000
```

Outcome: a single TCP SYN packet is sent to port 6000; the reply (SYN/ACK versus RST) is shown tcpdump-style, revealing the port state.

### Discover hosts that filter ICMP ping

Use ARP instead of ICMP to find live hosts.

```
nsoq -a 198.51.100.10
```

Outcome: an ARP request is sent for `198.51.100.10`; a live host answers with an ARP reply carrying its MAC/IP pair, discovering hosts behind ICMP filters.

### Ask the network who owns an address

Broadcast a who-has ARP request on a chosen interface.

```
nsoq -i eth0 -H ff:ff:ff:ff:ff:ff -d 198.51.100.10 -A
```

Outcome: an ARP REQUEST ("who has 198.51.100.10?") is broadcast on `eth0`; the owner replies with its MAC/IP pair, which nsoq displays.

### Move a file between hosts over TCP (netcat style)

Pair a sender with a listener.

```
nsoq -d target.example.com -p 12345 < /etc/hosts
```

Outcome: a TCP connection to port 12345 is opened and the file's contents transmitted; on the receiving side `nsoq -lC -P 12345 > file.txt` accepts the connection and writes the received data to a file.

### Listen for UDP or ARP traffic

Watch a port or the link layer.

```
nsoq -lU -P 59
```

Outcome: nsoq listens on UDP port 59 on all addresses and prints arriving packets; `-lA` listens for ARP/RARP on the link layer, and `-D` adds a full packet-content dump.

*No corpus evidence — derived from upstream documentation.*
