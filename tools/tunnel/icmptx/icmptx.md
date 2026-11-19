---
name: "icmptx"
categories:
  - blackarch-tunnel
version: "0.2.r12.g2235866"
homepage: "http://thomer.com/icmptx/"
packaged-by: [blackarch]
blackarch-builddate: "2025-02-23"
build-system: make
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "jakkarth/icmptx"
github-stars: 149
github-pushed: "2025-02-20"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# icmptx

> Minimal IP-over-ICMP tunnel: a point-to-point link between two root-owned hosts, carried entirely inside ICMP packets.

## What it's for

Moving IP traffic where only ping gets through. Both ends run icmptx as root; a TUN interface appears on each side and anything routed into it is encapsulated in ICMP. There is no encryption, authentication or padding — treat it as a raw serial link and layer SSH on top for anything sensitive. The lineage: ICMP handling from itunnel, TUN handling from VTun, first joined by edi/teso, cleaned up by Siim Põder, then maintained and published by Thomer Gil at thomer.com/icmptx; the current maintainer (jakkarth) hosts the project at Codeberg (`codeberg.org/jakkarth/icmptx`) after leaving GitHub — the BlackArch recipe builds from the Codeberg tree, while the GitHub mirror stays archived.

## Typical use cases

### Start the server end

On the host with the reachable public address:

```
icmptx -s 198.51.100.10 &
sleep 1
ifconfig tun0 10.0.3.1 netmask 255.255.255.0
```

Outcome: icmptx creates `tun0` and waits for echo packets addressed to 198.51.100.10. The `sleep` gives the interface time to appear before its address is set; replace 10.0.3.x with whatever inner addressing you want on the link.

### Connect the client end

On the host inside the ICMP-only-restricted network:

```
icmptx -c 198.51.100.10 &
sleep 1
ifconfig tun0 10.0.3.2 netmask 255.255.255.0
```

Outcome: `ping 10.0.3.1` now answers through the link — each side's `tun0` behaves like an ordinary point-to-point interface, and SSH to 10.0.3.1 works through the ICMP channel.

### Route a subnet through the link

On the client:

```
route add -net 192.168.0.0/24 gw 10.0.3.1
```

Outcome: traffic to 192.168.0.0/24 is transparently tunneled — the client reaches the server-side network through the ICMP carrier. Substitute any network reachable from the server end.

*No corpus evidence — derived from upstream documentation.*
