---
name: "proxyscan"
categories:
  - blackarch-scanner
version: "0.3"
homepage: "http://packetstormsecurity.com/files/69778/proxyScan.3.tgz.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# proxyscan

> proxyScan.pl — scans for hosts and ports reachable through a web proxy, mapping internal networks from the proxy's point of view.

## What it's for

Where proxycheck.pl fires a single CONNECT, proxyScan (0.3, 2008) automates the sweep: given a proxy and sets of targets and ports, it repeatedly asks the proxy to connect or fetch, reporting which internal hosts and ports are reachable through it — written specifically to take the legwork out of testing a misconfigured web proxy. The request method is selectable (CONNECT, GET, OPTIONS, TRACE, ...), targets accept single hosts, ranges and names, and per-request timeout and delay are tunable. Defaults are GET method against localhost:8080.

## Typical use cases

### Sweep a port range through a proxy

Find what the proxy can reach on a subnet.

```
perl proxyScan.pl -x 198.51.100.10:8080 -t 198.51.100.1-100 -p 22,80,443,3389
```

Outcome: for each target/port pair the proxy is asked to make the request (GET by default); reachable combinations are reported, producing an internal port map as seen from the proxy host.

### Use CONNECT instead of GET

Test the classic open-forward-proxy behavior.

```
perl proxyScan.pl -x 198.51.100.10:8080 -m CONNECT -t 198.51.100.10 -p 5985,5986
```

Outcome: probes use `CONNECT host:port`, so only true CONNECT-enabled proxying succeeds — the distinction that separates a reverse proxy from an open forward proxy.

### Tune pacing against slow or rate-limiting servers

Slow down when fast probes get dropped.

```
perl proxyScan.pl -x 198.51.100.10:8080 -t 198.51.100.1-25 -p 80 -o 5 -d 2 -v
```

Outcome: each request waits up to 5 seconds for a response (`-o`, default 2) and 2 seconds pass between requests (`-d`, default 0.5), with `-v` printing request/response detail for debugging misses.

*No corpus evidence — derived from upstream source (proxyScan-0.3.tgz from PacketStorm; options read from the usage text in `proxyScan.pl`).*
