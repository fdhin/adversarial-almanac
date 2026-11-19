---
name: "gatecrasher"
categories:
  - blackarch-recon
version: "2.3ad5225"
homepage: "https://github.com/michaeltelford/gatecrasher"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "michaeltelford/gatecrasher"
github-stars: 5
github-pushed: "2017-02-25"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gatecrasher

> Python 2 subnet discovery tool: broadcast ICMP, HTTP, SSH, MySQL or custom TCP/UDP probes across a class-C network ID and list every host that answers.

## What it's for

Gatecrasher (michaeltelford) is a small network auditing helper that discovers contactable hosts on a subnet by trying to reach every address in a network ID (the first three octets, e.g. `198.51.100`) and recording which ones respond. The probe protocol is chosen by keyword — ICMP (the default, needs root), HTTP, HTTPS, FTP, FTPS, SSH or MySQL — or as a raw `@tcp:PORT@` / `@udp:PORT@` spec for arbitrary ports; an optional third argument sets the reply timeout (default 0.25s, raise it for WANs). It also embeds a UDP echo server mode. It is Python 2 only (Python 3 is explicitly unsupported) and designed around class-C networks — larger ranges need manual splitting. It finds live hosts and listening services; it does not identify or exploit them.

## Typical use cases

### Ping-sweep a class-C network

Discover every ICMP-responsive host.

```
sudo gatecrasher 198.51.100
```

Outcome: broadcasts ICMP echo probes across `.0`–`.255` of the network ID with the default 0.25s timeout and prints the addresses that answered — run as root, since raw ICMP requires it.

### Find every web server on a subnet

Probe a specific service instead of ping.

```
gatecrasher 198.51.100 http
```

Outcome: contacts each address on the subnet as an HTTP client and prints only hosts that responded on the web ports; other usable protocol keywords are `https`, `ftp`, `ftps`, `ssh` and `mysql`, with an optional timeout third argument (e.g. `198.51.100 ssh 0.7`).

### Probe an arbitrary TCP port

Sweep a custom service port.

```
gatecrasher 198.51.100 @tcp:8080@
```

Outcome: attempts TCP connections to port 8080 on every address in the range and lists the ones that accept — a quick single-port sweep without a full port scanner; `@udp:PORT@` does the same for UDP (a longer timeout like `0.05` as third argument helps reliability).

### Run a UDP echo server

Use the built-in listener for reachability testing.

```
gatecrasher echo 8888
```

Outcome: binds UDP port 8888 and echoes back any data sent to it until interrupted with Ctrl+C — useful as a target endpoint for UDP probes from other hosts.

*No corpus evidence — derived from upstream documentation.*
