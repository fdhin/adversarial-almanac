---
name: "netscan2"
categories:
  - blackarch-scanner
version: "60.3d02ba1"
homepage: "https://github.com/walchko/netscan2"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "walchko/netscan2"
github-stars: 33
github-pushed: "2023-11-08"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netscan2

> Python library for mapping a LAN actively (ARP ping plus port scan) or passively (pcap/mDNS), fingerprinting hosts by MAC address.

## What it's for

netscan2 (Kevin Walchko, Python 2) builds a picture of the local network two ways. The active mapper ARP-pings the subnet (shelling out to arp-scan), resolves host names through avahi/dig, and port-scans each responding host. The passive mapper instead reads a pcap file or listens on an interface and reconstructs hosts from ARP and mDNS traffic without sending anything. Hosts are keyed by MAC address rather than IP (addresses drift, MACs are stable), and results are plain Python dicts you can dump to JSON or render into an HTML page. Note that the current package installs a library only — the console commands of earlier releases (`netscan`, `ascan`, `pscan`) are commented out of setup.py — so it is driven through the Python API; anything touching raw packets or arp-scan needs root. It shares nothing but a name with the C port scanner netscan.

No corpus evidence — derived from upstream documentation and the packaged source (setup.py, examples).

## Typical use cases

### Actively map a LAN

ARP-ping the subnet and port-scan everything that answers.

```
sudo python2 -c "
from netscan.ActiveScan import ActiveMapper
am = ActiveMapper(range(1, 1024))
hosts = am.scan('eth0')
print(hosts)"
```

Outcome: arp-scan finds live hosts on eth0, names are resolved via avahi, ports 1-1023 are scanned per host, and a list of host records (MAC, IPv4, hostname, open ports) is returned — ready to `json.dump` into a file for later comparison.

### Passively map from a capture

Reconstruct hosts from recorded traffic.

```
python2 -c "
from netscan.PassiveScan import PassiveMapper
pm = PassiveMapper()
hosts = pm.pcap('capture.pcap')
hosts = pm.filter(hosts)
hosts = pm.combine(hosts)
print(hosts)"
```

Outcome: ARP and mDNS packets in the pcap are decoded into host records, noise is filtered out and per-MAC records merged into one host list; `pm.live('eth0')` does the same against live traffic instead of a file.
