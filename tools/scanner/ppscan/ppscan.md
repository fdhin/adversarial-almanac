---
name: "ppscan"
categories:
  - blackarch-scanner
version: "0.3"
homepage: "https://packetstormsecurity.com/files/82897/PPScan-Portscanner.3.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ppscan

> PPScan — a parallel TCP port scanner that can scan through HTTP CONNECT proxies or FTP servers instead of connecting directly.

## What it's for

PPScan (0.3, 2009, by Aaron Conole) is a threaded port scanner whose distinguishing feature is tunneling: besides plain tcp-connect and tcp-syn scans, it can drive scans through an HTTP proxy that allows CONNECT, or through an FTP server that permits arbitrary port connections (FTP bounce). That lets you map non-routable subnets from a misconfigured edge host. Targets are single hosts or CIDR networks, ports are ranges or comma lists, and thread count is adjustable. There is no banner grabbing or HTTP-auth support.

## Typical use cases

### Scan a subnet through an HTTP proxy

Map a network as reachable from a proxy that permits CONNECT.

```
ppscan -x 198.51.100.1 -s http-connect -p 8080 -r 1-65535 -t 203.0.113.0/24 -T 3 -v
```

Outcome: for each host and port in the target /24, the scanner asks the proxy at 198.51.100.1:8080 to CONNECT and reports the open ones; `-T 3` caps concurrent scan threads and `-v` prints verbose progress.

### FTP bounce scan with credentials

Use an FTP server that allows arbitrary data connections as the relay.

```
ppscan -s ftp-port -x 198.51.100.10 -p 21 -u user -w Password1 -t 203.0.113.0/24 -r 21-23,80
```

Outcome: ports are probed by relaying through the FTP server with the given login (`-u`/`-w` default to anonymous), revealing which hosts and ports are open from the FTP host's point of view.

### Plain direct scan

Fall back to a conventional threaded connect scan with no relay.

```
ppscan -t 198.51.100.0/24 -r 1-1024
```

Outcome: default tcp-connect scanning of the range over the given port range — behaves like an ordinary parallel port scanner when no proxy or FTP relay is involved.

*No corpus evidence — derived from upstream source (PacketStorm ppscan-0.3 tarball; flags and all three example shapes verified against `dump_help()` and getopt in `ppscan.c`).*
