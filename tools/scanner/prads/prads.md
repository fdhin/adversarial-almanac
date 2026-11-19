---
name: "prads"
categories:
  - blackarch-scanner
version: "1132.e631f4f"
homepage: "http://gamelinux.github.io/prads/"
packaged-by: [blackarch]
blackarch-builddate: "2021-12-13"
build-system: make
license: PerlArtistic
meta-checked: "2026-11-09"
github-repo: "gamelinux/prads"
github-stars: 249
github-pushed: "2024-05-28"
github-archived: false
github-issues: 8
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# prads

> PRADS — Passive Real-time Asset Detection System: builds a host, service and OS inventory by sniffing traffic without ever sending a packet.

## What it's for

PRADS passively listens to network traffic and records every asset it observes — IP, VLAN, port, protocol, service, and (from SYN/SYNACK fingerprints) a guessed OS and TTL-based distance — appending time-stamped CSV lines to an asset log. It is the quiet alternative to active scanning for networks you must not disturb, and its output is designed to feed other tooling: the bundled `prads2snort` script converts the log into Snort host-attribute XML, events can be piped through a FIFO to consumers such as the sguil pads agent, and a long-running log becomes an anomaly-detection baseline. It reads a live interface or a saved pcap file.

## Typical use cases

### Inventory a live network segment

Quietly build an asset list while traffic flows.

```
prads -i eth0 -l prads.log
```

Outcome: appends CSV lines of the form `asset,vlan,port,proto,service,[fingerprint],distance,timestamp` to `prads.log` for every host and service seen on eth0 — for example a SYN fingerprint line identifying an OS family, and ARP lines with MAC vendor guesses.

### Inventory from a stored capture

Work offline against pcaps you already collected.

```
prads -r capture.pcap -l assets.log
```

Outcome: processes the capture file instead of a live interface and produces the same asset CSV — a way to retroactively inventory traffic captured during an incident.

### Restrict collection with a BPF filter

Limit what gets parsed and fingerprinted.

```
prads -i eth0 -b "port 80 or port 443" -l web-assets.log
```

Outcome: only web traffic is parsed, so the log covers HTTP/HTTPS services rather than everything on the wire.

### Stream events to a FIFO

Hand asset events to a consuming agent in real time.

```
prads -i eth0 -f /tmp/prads.fifo
```

Outcome: events are written to the named FIFO as they happen — the documented way to feed PRADS output into sguil-style tooling instead of a flat file.

*No corpus evidence — derived from upstream source (README at gamelinux/prads; `-i`/`-r`/`-b`/`-l`/`-f` semantics verified against `src/config.c`).*
