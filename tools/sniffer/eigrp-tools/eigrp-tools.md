---
name: "eigrp-tools"
categories:
  - blackarch-sniffer
version: "0.1"
homepage: "http://www.hackingciscoexposed.com/?link=tools"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-24"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# eigrp-tools

> Perl EIGRP packet generator and sniffer from the "Hacking Exposed: Cisco Networks" toolkit.

## What it's for

eigrp-tools packages `eigrp.pl`, a Perl 5 EIGRP packet generator and sniffer written by Janis Vizulis at Arhont Ltd while working on the book "Hacking Exposed: Cisco Networks". Its purpose is auditing the security and operational quality of Cisco EIGRP deployments: it forges hello, update and query packets, injects fake routes, replays captured EIGRP data, and sends "goodbye" messages that tear down neighbor adjacencies. Packet defaults (AS number, K-values, metrics, routes) come from the bundled `eigrp.conf`, which must be reviewed and edited before use. It assumes you already know the audited network's EIGRP autonomous-system number and Layer-3 topology, and it ships proof-of-concept denial-of-service modes (including a hello-flood range attack and a sniff-modify-reinject "payback" PoC) that can stall an entire routing domain — run it only on networks you are authorized to test. It is not a router scanner or discovery tool. On BlackArch the `eigrp` wrapper runs `eigrp.pl` from `/usr/share/eigrp`, where the config lives.

## Typical use cases

### Dissect live EIGRP traffic

Watch and decode EIGRP packets on a segment, including "goodbye" packets that Ethereal-era dissectors did not recognize.

```
sudo eigrp --sniff --iface eth0
```

Outcome: prints a field-by-field dissection of every EIGRP packet seen on eth0 (opcode, sequence/ack, AS number, K-values, TLVs). Stop with Ctrl-C.

### List usable capture interfaces

```
eigrp --iflist
```

Outcome: prints each interface name with its IP address so you can choose the `--iface` value for sniffing and the spoofed `--source` address for injection.

### Spoof an EIGRP peer with forged hellos

Announce a fake neighbor so adjacent routers record an adjacency toward an address you control.

```
sudo eigrp --hello --as 100 --source 198.51.100.10
```

Outcome: repeatedly sends EIGRP hello packets (opcode 5) sourced from 198.51.100.10 for AS 100 (retry interval from `--hellotime`, default 5s); neighboring routers begin treating that address as a peer.

### Inject a fake external route

```
sudo eigrp --update --external --as 100 --source 198.51.100.10 --routedest 198.51.100.0/24
```

Outcome: crafts an EIGRP external route update (opcode 1) for 198.51.100.0/24 using the metric and external-protocol values from `eigrp.conf`/overrides; accepting neighbors install the forged route.

### Drop a neighbor adjacency with a goodbye message

```
sudo eigrp --ipgoodbye 198.51.100.20 --source 198.51.100.10
```

Outcome: sends an EIGRP goodbye (hold-time 0) naming 198.51.100.20, causing routers to drop the affected adjacency — a clean denial-of-service proof of concept.

### Replay captured EIGRP packets

```
sudo eigrp --file2ip update.dat --source 198.51.100.10
```

Outcome: reads raw sniffed EIGRP data previously saved to `update.dat` and re-injects it onto the wire from the spoofed source address, reproducing the original packet exactly.

*No corpus evidence — derived from upstream documentation.*
