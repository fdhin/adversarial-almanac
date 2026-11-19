---
name: "phantap"
categories:
  - blackarch-networking
version: "65.e8d10ea"
homepage: "https://github.com/nccgroup/phantap"
packaged-by: [blackarch]
blackarch-builddate: "2025-10-12"
build-system: cmake
license: MIT
meta-checked: "2026-11-09"
github-repo: "nccgroup/phantap"
github-stars: 626
github-pushed: "2025-10-06"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# phantap

> An 'invisible' inline network tap for OpenWrt routers that masks itself as the victim device.

## What it's for

PhanTap (Phantom Tap) is an NCC Group project that turns a small two-port OpenWrt router into a stealthy inline tap: installed between a victim device and the corporate network, it bridges transparently, stays silent (no ARP, multicast, or broadcast of its own; 802.1X passes through), and does not disturb the victim's traffic. Its phantap-learn daemon watches passing traffic to learn the victim's IP/MAC, gateway, and DNS server, then SNATs its own outbound traffic behind the victim's identity — typically to bring up a VPN tunnel back to the operator for further analysis and pivoting. Deployment is on the OpenWrt device itself (commands below run there; the packaged source ships the init scripts and /etc tree); configuration lives in /etc/config/phantap and the uci network config. It is not a workstation monitoring tool — it requires physically inserting the device in-line.

## Typical use cases

### One-time setup on the tap device

Bootstrap the PhanTap service on a freshly flashed OpenWrt router.

```
/etc/init.d/phantap setup
```

Outcome: creates the phantap bridge network configuration if it is missing; on service start the phantap-learn daemon runs on br-phantap and blocks the device's own egress until a victim is detected.

### Bridge the two physical ports into the tap

Attach both interfaces to the silent bridge.

```
uci add_list network.br_phantap.ports='eth0'
uci add_list network.br_phantap.ports='eth1'
uci commit network
/etc/init.d/network reload
```

Outcome: both physical ports join the transparent bridge; once cabled inline between a victim machine and its switch, the victim's traffic forwards untouched while the tap stays invisible on the wire.

### Run a command whenever the network is (re)learned

Re-establish the reverse tunnel automatically.

```
uci add_list phantap.main.onnetconfig='/etc/init.d/openvpn restart'
uci commit phantap
```

Outcome: every time PhanTap learns or re-learns the victim network configuration (including IP or DNS changes) the hook command executes — e.g. restarting the VPN that carries the operator's tunnel (use ondnsconfig for DNS-change hooks).

*No corpus evidence — derived from upstream documentation.*
