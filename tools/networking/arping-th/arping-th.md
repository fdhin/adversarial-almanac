---
name: "arping-th"
categories:
  - blackarch-networking
version: "2.25"
homepage: "https://github.com/ThomasHabets/arping"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-09-04"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "ThomasHabets/arping"
github-stars: 444
github-pushed: "2026-10-30"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 189
gh-activity-checked: "2026-11-09"
---

# arping-th

> ARP-level ping by Thomas Habets — discover if an IP is taken or which IP owns a MAC, where ICMP cannot reach.

## What it's for

`arping` (Thomas Habets; packaged as `arping-th` so it does not collide with the iputils `arping` of the same name) sends ARP requests and replies directly through libnet/libpcap. Unlike ICMP ping it works at layer 2: hosts that block all ICMP still answer ARP, and on unrouted networks you can probe which addresses are in use before configuring your own. You can ping an IP to learn the MAC that owns it, or ping a MAC to discover its IPs; it can also send unsolicited ARP to (re)announce an address, and produce script-friendly raw output. It needs root (or the `CAP_NET_RAW` capability) and only reaches the local network — ARP cannot cross routers.

## Typical use cases

### Check whether an IP address is in use

```
sudo arping-th -c 3 198.51.100.10
```

Outcome: sends three ARP requests and prints the replying MAC with per-reply timing and TTL info; silence means the address is free to configure — the classic check before assigning yourself a static IP.

### Find the IP behind a MAC address

```
sudo arping-th -c 3 00:11:85:4c:01:01
```

Outcome: pings the MAC itself (this relies on the host answering a broadcast ping) and replies reveal which IP that machine owns — how you identify a box dumping unknown frames on your segment. If it stays silent, restrict the query to its subnet with `-T` (e.g. `-T 198.51.100.255`).

### Raw output for scripts

```
sudo arping-th -C 2 -c 10 -r 198.51.100.10
```

Outcome: `-r` prints only the answering MAC (`-R` adds the "other" address of the pair) and `-C` stops after two replies even though ten may be sent — output trimmed for shell pipelines and monitoring probes.

### Re-announce an address after a failover

```
sudo arping-th -i eth0 -U 198.51.100.10
```

Outcome: sends unsolicited ARP replies for the interface's address (`-U`), forcing neighboring machines to refresh their ARP caches after a failover or interface swap instead of waiting for stale entries to expire.

*No corpus evidence — derived from upstream documentation.*
