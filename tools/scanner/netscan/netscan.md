---
name: "netscan"
categories:
  - blackarch-scanner
version: "1.0"
homepage: "http://packetstormsecurity.com/files/125569/Netscan-Port-Scanner.0.html"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2018-11-15"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# netscan

> Single-host port scanner in C with SYN, TCP-connect, UDP and Tor (SOCKS5) modes plus banner grabbing.

## What it's for

netscan (the packetstorm-released C scanner, built with pthreads) checks one host at a time using one of four methods: SYN half-open scanning (root only), full TCP connect, UDP, or scanning through a local Tor SOCKS5 proxy on 127.0.0.1:9050 so probes exit via Tor. Port selection takes three forms — a single port, a range (`-p 1-1000`) or a comma list (`-p 22,80,443`) — and without `-p` it scans the ports listed in /etc/services. Banners can be grabbed from open ports with `-b`. It is a quick single-target checker with a live progress display and end-of-scan statistics, not a subnet inventory tool. Despite the name it is unrelated to netscan2, the Python active/passive mapper.

No corpus evidence — derived from the packaged upstream source (netscan.c help and option parsing).

## Typical use cases

### SYN scan a host

Half-open scan without completing connections.

```
sudo netscan -s 198.51.100.10
```

Outcome: raw-socket SYN scan (refuses to run as non-root) of the /etc/services port set, classifying each port open/closed/filtered and ending with a statistics line.

### Connect scan a port range with banners

Full TCP connections plus service identification.

```
netscan -c -b -p 1-1000 198.51.100.10
```

Outcome: TCP connect scan of ports 1-1000; for every open port the service banner is retrieved and printed (including HTTP Server headers), alongside the open/closed/filtered tally.

### Scan a host through Tor

Route probes through the local Tor SOCKS5 proxy.

```
netscan -t example.com
```

Outcome: all probes connect via 127.0.0.1:9050 (Tor's default SOCKS port), so the target sees Tor exit traffic; requires a running Tor service.

### UDP scan a host

Probe UDP services.

```
netscan -u 198.51.100.10
```

Outcome: UDP probes with built-in resends (six attempts per port) report which UDP ports answer; `-d N` spaces SYN packets out for slower links and `-v` adds verbose output.
