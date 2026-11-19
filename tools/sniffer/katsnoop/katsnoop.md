---
name: "katsnoop"
categories:
  - blackarch-sniffer
version: "0.1"
homepage: "http://packetstormsecurity.com/files/52514/katsnoop.tbz2.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: other
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# katsnoop

> Prints HTTP Basic authentication credentials, base64-decoded, as they cross an Ethernet segment.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A single-purpose libpcap sniffer: watch TCP packets on an Ethernet interface for an HTTP `Authorization` header and print the timestamp, the source and destination host:port pair, and the base64-decoded `user:password` behind it. It does nothing else — no output files, no other protocols, no capture support. Written by Kris Katterjohn in 2006 as a sibling of his katsniff; it needs root, only understands Ethernet framing, and is useful on shared-medium or mirrored segments where cleartext HTTP logins are visible.

## Typical use cases

### Sniff HTTP basic auth on a specific interface

```
katsnoop -i eth0
```

Outcome: with root privileges eth0 is opened promiscuously and all TCP traffic is scanned; every packet carrying an Authorization header produces a line like `[MM/DD/YYYY HH:MM:SS] host:port -> host:port` followed by the decoded `user:password`.

### Run without hostname resolution

```
katsnoop -i eth0 -d
```

Outcome: identical capture, but endpoints are printed as numeric IP addresses — skips the reverse-DNS lookup per packet, avoiding stalls on addresses that do not resolve.
