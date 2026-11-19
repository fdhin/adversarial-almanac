---
name: "pbscan"
categories:
  - blackarch-scanner
version: "10.566c3d7"
homepage: "https://github.com/gvb84/pbscan"
packaged-by: [blackarch]
blackarch-builddate: "2018-10-01"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "gvb84/pbscan"
github-stars: 172
github-pushed: "2018-02-20"
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

# pbscan

> Stateless SYN scanner and banner grabber built on a userland TCP/IP stack.

## What it's for

polarbear scan (pbscan) combines two techniques for fast reconnaissance: stateless SYN scanning with cryptographically protected cookies (in the spirit of Dan Kaminsky's scanrand) to detect open ports, and a patched userland TCP/IP stack (libuinet, a port of FreeBSD's stack) that completes the three-way handshake from the received SYN-ACK itself. That means it goes straight from "port open" to a fully established connection for banner grabbing or probing — no second, kernel-level connect like nmap's version scan needs. Four scan types: passive banner grab, HTTP request, TLS probe and custom payload. IPv4 over Ethernet-type interfaces only; must run as root.

## Typical use cases

### Banner-grab a subnet

Collect service banners at SYN speed.

```
sudo pbscan -sB -p22,80,143,8080-9000 -b300k 198.51.100.0/24
```

Outcome: SYN-scans the CIDR on the listed ports (singles and ranges, comma-separated) with outgoing probes capped at 300 kbps, then for each open port completes the connection through the userland stack and prints the service's first banner line.

### Identify HTTP servers

Send an HTTP request during the scan.

```
sudo pbscan -sH -p80,443,8080 198.51.100.0/24
```

Outcome: every open port that accepts the connection receives a `GET / HTTP/1.1` request and the response head is printed — a fast census of web servers across a range.

### Probe TLS services

Check which ports speak TLS.

```
sudo pbscan -sT -p443,8443 198.51.100.0/24
```

Outcome: a TLS 1.0 null probe is sent to each established connection and parsed TLS error responses are displayed, distinguishing TLS listeners from plaintext services.

### Fire a custom payload from a file

Probe a specific protocol with your own bytes.

```
sudo pbscan -sC -d payload.bin -p5555 198.51.100.10
```

Outcome: the contents of payload.bin (up to 128 kB) are sent over each established connection and responses are printed; `-x` forces hex-dump output, and `-t` sets how long to keep reading after the probe wave finishes.

No corpus evidence — derived from upstream documentation (gvb84/pbscan README usage section).
