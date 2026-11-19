---
name: "dhcdrop"
categories:
  - blackarch-misc
version: "0.5"
homepage: "http://www.netpatch.ru/dhcdrop.html"
packaged-by: [blackarch]
blackarch-builddate: "2015-01-29"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/dhcdrop"
github-stars: 0
github-pushed: "2019-09-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dhcdrop

> Discovers DHCP servers on a segment and takes rogue ones offline by exhausting their address pools.

## What it's for

dhcdrop (by Roman Chebotarev, netpatch.ru) tackles the rogue-DHCP problem: a misconfigured SOHO router plugged into the LAN starts handing out wrong addresses to clients. The tool puts an interface in promiscuous mode, sends DHCPDISCOVER packets from randomized client MACs, and reports every server that answers — its IP, Ethernet address, lease time and the addresses it offers. Suppression works by pool exhaustion: it requests and leases every address in the rogue server's dynamic pool under unique fake MACs, so the server runs dry and stops serving real clients until leases expire. It can also send DHCPRELEASE messages (`-R`) and ARP-scan subnets (`-S`) to survey clients that already drew bad configuration. Needs root and libpcap; run it only on networks you administer.

## Typical use cases

### List capture interfaces

```
sudo dhcdrop -D
```

Outcome: prints every usable interface with its index and assigned addresses; the index or name feeds `-i` (essential on Windows, where interfaces carry long NPF device names).

### Hunt servers interactively on a segment

```
sudo dhcdrop -i eth1
```

Outcome: for each responding DHCP server, prints its IP, Ethernet address, lease time and offered client IP, then asks `Drop him? [y/n]`; answering `y` drains that server's pool, `n` moves on to the next responder.

### Suppress every server except the legitimate one

```
sudo dhcdrop -i eth1 -y -l 00:11:22:33:44:55
```

Outcome: `-l` names the legal server's MAC (left untouched) and `-y` skips the prompts — every other DHCP responder is drained, each acquired lease ticking past as a numbered DHCPACK line until offers time out.

### Aggressive mode: also release leases already handed out

```
sudo dhcdrop -i eth1 -y -l 00:11:22:33:44:55 -L 198.51.100.0
```

Outcome: after draining the pool, ARP-scans the given legitimate network, identifies hosts that drew addresses from the rogue server, sends DHCPRELEASE on their behalf, waits (default 60 s) and repeats.

### Send a single crafted DHCPRELEASE

```
sudo dhcdrop -i eth1 -R -s 198.51.100.10 -F 198.51.100.50 00:11:22:33:44:55
```

Outcome: emits one DHCPRELEASE stating that client 00:11:22:33:44:55 releases 198.51.100.50 (`-F`) to server 198.51.100.10 (`-s`), then prints `Finished.`.

### ARP-scan a subnet for affected clients

```
sudo dhcdrop -i eth1 -S 198.51.100.0/24
```

Outcome: ARP-scans the subnet directly on the interface regardless of the host's own routing, listing each responding MAC and IP; add `-F` to pin the source IP instead of letting it pick a random one.

*No corpus evidence — derived from upstream documentation.*
