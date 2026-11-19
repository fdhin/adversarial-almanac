---
name: "batman-adv"
categories:
  - blackarch-wireless
version: "2019.2"
homepage: "http://www.open-mesh.net/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-05-26"
build-system: make
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "open-mesh-mirror/batman-adv"
github-stars: 202
github-pushed: "2026-11-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 339
gh-activity-checked: "2026-11-09"
---

# batman-adv

> Linux kernel module implementing B.A.T.M.A.N. — layer-2 mesh routing that makes all mesh nodes behave like one virtual switch (`bat0`).

## What it's for

batman-adv ("Better Approach To Mobile Ad-hoc Networking — advanced") is a kernel driver that builds a mesh network at the Ethernet layer: instead of exchanging routes over IP, it bridges Ethernet frames between participating nodes, so every node appears link-local to every other and any higher-layer protocol (IPv4, IPv6, DHCP) runs unchanged on top. It works over any Ethernet-style interface — Wi-Fi, wired, VPN — and is mainline since kernel 2.6.38, so on modern systems the module ships with the kernel and only needs loading. This is mesh-networking infrastructure, not a wireless attack tool. Its userspace companions are `batctl` (settings, ping/traceroute/debug tables) and the [[batman-alfred]] data-distribution daemon.

## Typical use cases

### Load the module and create a mesh interface

Activate batman-adv and instantiate a mesh.

```
sudo modprobe batman-adv
sudo ip link add name bat0 type batadv
```

Outcome: the module loads and a `bat0` interface appears; module messages (all prefixed `batman-adv:`) go to the kernel log, checkable with `dmesg | grep batman-adv`.

### Attach hard interfaces and address the mesh

Enslave a radio and bring the mesh up with an IP.

```
sudo ip link set dev wlan0 master bat0
sudo ip addr add 198.51.100.1/24 dev bat0
sudo ip link set up dev bat0
```

Outcome: batman-adv starts sending originator messages out `wlan0` (flush any IP previously on `wlan0` with `ip addr flush dev wlan0`); with the same steps and `.2` on a second node, `ping` between the two `bat0` addresses flows over the mesh regardless of how many hops separate them.

### Tune the originator interval

Make the mesh react faster to mobile nodes.

```
sudo batctl -m bat0 orig_interval 3000
```

Outcome: read back with `batctl -m bat0 orig_interval` — lowers (or raises) how often broadcast originator packets are sent; lower values make the mesh converge faster on topology changes at the cost of more overhead (default 1000 ms).

### Inspect mesh counters

Read per-packet-type statistics for the mesh interface.

```
ethtool --statistics bat0
```

Outcome: prints the module's counters for each packet class entering and leaving batman-adv — useful when diagnosing whether broadcasts, unicast or fragmentation dominate a misbehaving mesh.

*No corpus evidence — derived from upstream documentation.*
