---
name: "schnappi-dhcp"
categories:
  - blackarch-misc
version: "0.1"
homepage: "http://www.emanuelegentili.eu/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# schnappi-dhcp

> Recover a usable IP address and default gateway on a LAN whose DHCP server is gone.

## What it's for

A small 2009 bash script by Emanuele Gentili for the "network with no DHCP" situation: you are on a segment where the DHCP server is unreachable and you need a working address and gateway without waiting for one. You give it an interface, a /24 range to sweep, and a static IP to claim; it configures the interface with that IP, ping-sweeps the range with nmap for live hosts, then repeatedly rewrites the default route to try each live host as gateway until one actually routes (verified by pinging an external resolver). Despite the name it is neither a DHCP client nor a rogue DHCP server — it does DHCP's job manually with static configuration. It needs root plus legacy `ifconfig`/`route` net-tools and nmap, so on modern systems using iproute2 those commands may be missing; verify whatever it reports before relying on the chosen gateway.

## Typical use cases

### Claim an IP and find a working gateway

```
schnappi-dhcp --interface eth0 --IPrange 198.51.100 --IPtest 198.51.100.10
```

Outcome: brings `eth0` up with the test address, sweeps 198.51.100.1-255 for live hosts, and for each candidate deletes the current default route and installs the candidate as gateway; on the first gateway whose external ping succeeds it stops and prints a NETWORK RESUME with your IP and the working gateway. Run as root.

### Point the host at working resolvers

Swap in public resolvers once connectivity is recovered (or when only DNS is broken).

```
schnappi-dhcp --dnsadd
```

Outcome: overwrites `/etc/resolv.conf` with the two OpenDNS nameservers (208.67.222.222 and 208.67.220.220) and confirms with `[*] DNS Added.` — any existing resolver configuration is replaced, back up the file first if it matters.

*No corpus evidence — derived from upstream documentation.*
