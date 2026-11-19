---
name: "nemesis"
categories:
  - blackarch-networking
version: "331.5aee868"
homepage: "https://github.com/libnet/nemesis"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-07-20"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "libnet/nemesis"
github-stars: 530
github-pushed: "2023-09-29"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nemesis

> Command-line TCP/IP stack for crafting and injecting arbitrary packets — one subcommand per protocol, built for shell-script-driven injection.

## What it's for

Nemesis is a portable "human IP stack": a single binary with a protocol subcommand each for ARP/RARP, DNS, DHCP, ETHERNET, ICMP, IGMP, IP, OSPF, RIP, TCP and UDP, where every header field has a flag, payloads can be read from files (`-P`), and IP/TCP options from files or stdin (`-O`). Built on libnet, it injects at layer 2 or layer 3 on UNIX-like systems, and every injector has its own man page. It is designed for scripted protocol testing — firewall and IDS behavior, routing/multicast daemons, stack robustness — and needs root for raw sockets. Created by Mark Grimes in 1999, maintained by Jeff Nathan from 2001, and resurrected by Joachim Nilsson in 2018; the project now lives under the libnet organization on GitHub.

## Typical use cases

### Inject an ICMP redirect

```
sudo nemesis icmp -S 198.51.100.3 -D 198.51.100.1 -G 198.51.100.3 -i 5
```

Outcome: injects an ICMP redirect packet (type `-i 5`) claiming that traffic should be rerouted via the gateway address given with `-G` — the direct way to test whether hosts honor unsolicited redirects.

### Fire a DHCPDISCOVER from address 0.0.0.0

```
sudo nemesis dhcp -d eth0
```

Outcome: broadcasts a DHCPDISCOVER on eth0 with the unspecified source address (which is why `-d` is required); run a sniffer alongside to watch which DHCP servers offer, including rogue ones.

### Forge an IGMPv2 group join

```
sudo nemesis igmp -v -p 22 -S 198.51.100.20 -g 239.186.39.5 -D 239.186.39.5
```

Outcome: injects a membership report for the multicast group, exercising IGMP snooping and querier behavior on a switch without a real member; `-p 0x11` forges queries instead.

### Inject a TCP packet with chosen flags and payload

```
sudo nemesis tcp -v -S 198.51.100.1 -D 198.51.100.2 -fSA -y 22 -P payload.txt
```

Outcome: sends a hand-built TCP packet bearing SYN+ACK (`-fSA`) to port 22 carrying the contents of payload.txt; `-v` prints the packet as it is assembled. Swapping the flag mask (`-fR`, `-fA`, ...) probes firewall state-table handling.

### Inject a crafted ARP frame

```
sudo nemesis arp -v -d eth0 -H 0:1:2:3:4:5 -S 198.51.100.5 -D 198.51.100.1
```

Outcome: puts a fully specified ARP frame on the wire via eth0 with the given Ethernet and protocol addresses — used to test IDS ARP rules and static-ARP defenses without an ARP-spoofing framework.

*No corpus evidence — derived from upstream documentation.*
