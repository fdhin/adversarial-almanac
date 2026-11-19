---
name: "multimac"
categories:
  - blackarch-spoof
version: "1.0.3"
homepage: "http://sourceforge.net/projects/multimac/"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Seabreg/multimac"
github-stars: 0
github-pushed: "2019-05-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# multimac

> Emulates N virtual machines on a single adapter — allocates TAP interfaces that each carry their own MAC address and bridges them onto the real LAN link.

## What it's for

Gives one Linux machine multiple layer-2 identities: where IP aliases (`eth0:0`) cannot have their own MAC address, multimac creates TAP interfaces that can. It was built for network developers who need to emulate the presence of several machines (each with a distinct MAC and IP) from a single network adapter, and started life as a test of the Linux Tun/Tap driver. It requires the `tun` and `bridge` kernel modules. It multiplies identities rather than replacing one — it is not a MAC changer — and each virtual interface behaves as a separate host on the LAN once bridged.

## Typical use cases

### Allocate a pool of virtual interfaces

```bash
sudo modprobe tun
sudo modprobe bridge
sudo ./multimac 5
```

Outcome: allocates six TAP interfaces, `tap0` through `tap5`. `tap0` is the hub — all traffic generated on `tap1`..`tap5` is cloned onto it — while each of `tap1`..`tap5` will be configured as an independent virtual host.

### Bridge the pool onto the real LAN and bring the virtual hosts up

```bash
brctl addbr br0
brctl addif br0 eth0
brctl addif br0 tap0
ifconfig eth0 0.0.0.0 up
ifconfig tap0 0.0.0.0 up
ifconfig br0 198.51.100.10 up
ifconfig tap1 hw ether 00:11:22:33:44:55
ifconfig tap1 198.51.100.11 up
```

Outcome: after the bridging driver settles, traffic sent on `tap1` leaves `eth0` with its own source MAC, so the LAN sees an additional machine; repeat the last two lines per tap with distinct MACs/IPs to stand up as many hosts as were allocated. Anything doing per-MAC accounting, discovery, or filtering now counts each tap as a separate device.

*No corpus evidence — derived from upstream documentation.*

GitHub: third-party mirror of the SourceForge upstream (tree byte-identical to the packaged `multimac.tar.gz` distfile).
