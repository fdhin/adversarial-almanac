---
name: "find-dns"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "https://packetstormsecurity.com/files/132449/Find-DNS-Scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# find-dns

> Threaded DNS-server finder by "dash": hand it a list of IPs and it fires a DNS query at each, reporting which hosts actually answer on port 53.

*No corpus evidence — derived from upstream documentation.*

## What it's for

find-dns answers one question across a whole address list: which of these machines run a reachable DNS service? It reads an IP list, sends a crafted DNS request (for a domain you can choose, `google.com` by default) to each address from a pool of worker threads, and tabulates the hosts whose replies prove a live resolver, optionally reverse-resolving them to a name. It is a pure Python (2) single script originally distributed via Packet Storm; it identifies servers, it does not enumerate zones or test resolver configuration.

## Typical use cases

### Sweep an IP list for live DNS servers

```
find-dns -l ips.txt -t 100
```

Outcome: with 100 threads, every IP in the list receives a DNS request; the terminal prints an IP/NAME table of hosts that answered (names resolved when possible) between `[*] Found N entries` and `[*] Done` markers.

### Write the findings to a file

```
find-dns -l ips.txt -t 500 -o dnsservers.txt
```

Outcome: the same sweep with 500 threads, and the responding DNS servers are also written to `dnsservers.txt` for later use as a resolver list.

### Use a custom probe domain without reverse lookups

```
find-dns -l ips.txt -d example.com -n
```

Outcome: the probe query asks for `example.com` instead of the default, and `-n` skips resolving the found IPs to names — a faster, quieter sweep. An experimental `-v` additionally attempts to fingerprint the server's version (the author marks it experimental; `--debug` shows the exchange).
