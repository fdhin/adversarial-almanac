---
name: "netcommander"
categories:
  - blackarch-spoof
version: "1.3"
homepage: "https://github.com/evilsocket/netcommander"
packaged-by: [blackarch]
blackarch-builddate: "2020-03-04"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "evilsocket/netcommander"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# netcommander

> One-shot ARP spoofing tool — enumerates live LAN hosts, then either silently relays their traffic through you (MITM) or cuts their connectivity.

## What it's for

NetCommander (Simone Margaritelli, evilsocket) packages ARP-based interception into a single scapy script. It arps the target network to enumerate live endpoints (resolving each MAC to a vendor via a prefix table), identifies the gateway, then poisons each endpoint's ARP cache so its traffic flows through your machine. Two modes: with IP forwarding enabled the traffic is silently relayed (a transparent MITM position); with `-K` forwarding never happens and the target's connection is killed instead. Runs on Linux and macOS; needs root and scapy. The upstream repository is gone — BlackArch vendors the `netcmd` script, installed as `netcommander`, with the mac-prefixes table under `/usr/share/netcommander/`.

## Typical use cases

### Enumerate a subnet and MITM every host

```bash
sudo netcommander -I eth0 -N 198.51.100.0/24 -G 198.51.100.1
```

Outcome: prints the alive hosts found by broadcast arping (with MAC vendor for each), enables IP forwarding, then repeatedly ARP-poisons every non-gateway host against the gateway at the default 5-second interval (`-D` changes it). Their traffic now transits this machine and is forwarded on.

### Cut a LAN's connectivity and keep new joiners down

```bash
sudo netcommander -I eth0 -N 198.51.100.0/24 -G 198.51.100.1 -K -A
```

Outcome: with `-K`, hosts are poisoned but traffic is never forwarded, so each target loses connectivity to the gateway; `-A` keeps the poisoning running and automatically attacks hosts that join the network later. Drop `-K` to flip the same run into forwarding mode.

*No corpus evidence — derived from upstream documentation.*
