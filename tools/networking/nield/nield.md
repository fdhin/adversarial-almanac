---
name: "nield"
categories:
  - blackarch-networking
version: "38.0c0848d"
homepage: "http://nield.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2019-01-28"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "t2mune/nield"
github-stars: 32
github-pushed: "2024-10-07"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nield

> Network Interface Events Logging Daemon: turns Linux kernel netlink events into timestamped logs of interface, ARP/NDP, address, route, FIB-rule and traffic-control changes.

## What it's for

nield subscribes to the Linux kernel's netlink socket and writes a timestamped log line for every network state change on the host: interfaces going up/down or being created/deleted (including VLAN, bridge, bonding, GRE and tunnel interfaces), ARP/NDP neighbor-cache churn, IPv4/IPv6 address assignment, route and FIB-rule additions/removals, and traffic-control qdisc/class/filter changes. Use it to audit or troubleshoot live network reconfiguration — catching an automation tool flapping routes, or documenting exactly when an interface's MTU, link address or bonding membership changed. It only observes and logs; it never modifies network state, and it is Linux-only by design (netlink).

## Typical use cases

### Log all network events to the default file

Start the daemon with no event filter.

```
nield
```

Outcome: the daemon runs and appends timestamped lines such as `interface eth0 state changed to up` or `arp cache added: ip=198.51.100.10 mac=00:1b:8b:84:36:dc interface=eth0` to `/var/log/nield.log` (the default when no logging option is given).

### Log only interface and neighbor-cache events

Restrict output to two event families.

```
nield -i -n
```

Outcome: only interface events (`-i`) and ARP/NDP neighbor-cache events (`-n`) are logged; with no event option all families are captured, and any option given limits logging to the selected families.

### Send events to syslog instead of a file

Route the log through syslog.

```
nield -L daemon
```

Outcome: events are delivered to syslog under the `daemon` facility rather than written to `/var/log/nield.log`; standard facility names (auth, daemon, local0-local7, ...) are accepted.

### Watch IPv4 routing changes only

Combine the protocol-family and event-type filters.

```
nield -4 -r
```

Outcome: logs only IPv4 routing events, e.g. `ipv4 route added: destination=203.0.113.0/24 nexthop=198.51.100.2 interface=eth0 type=unicast protocol=boot table=main`.

*No corpus evidence — derived from upstream documentation.*
