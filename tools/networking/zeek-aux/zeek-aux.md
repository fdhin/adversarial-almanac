---
name: "zeek-aux"
categories:
  - blackarch-networking
version: "794.f1f4bc5"
homepage: "https://github.com/zeek/zeek-aux"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-28"
build-system: cmake
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "zeek/zeek-aux"
github-stars: 28
github-pushed: "2026-10-27"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 41
gh-activity-checked: "2026-11-09"
---

# zeek-aux

> Auxiliary programs around the Zeek Network Security Monitor: adtrace, rst, and the zeek-archiver log-rotation service.

## What it's for

zeek-aux is the companion repository of small utilities for the Zeek Network Security Monitor ([[zeek]]) — it is not a monitor itself. It ships `adtrace`, which inventories the endpoints in a pcap; `rst`, which forges TCP reset packets to tear down a connection (the primitive a Zeek script invokes to kill a flow); and `zeek-archiver`, a modern replacement for Zeek's historical log-archival process that rotates and compresses logs atomically, independent of ZeekControl and built for the Supervisor Framework (ships a systemd unit; compression is selectable via `--compression=`). A devel-tools directory (connection extraction by conn.log UID, benchmark scripts, cert-list generation) exists for Zeek development but is not installed by `make install`.

## Typical use cases

### Inventory the endpoints in a capture

See which MAC/IP pairs a pcap contains.

```
adtrace capture.pcap
```

Outcome: Reads the tcpdump-format file and writes out the source MAC, destination MAC, source IP and destination IP for each packet — a quick view of the internal and external addresses composing the traffic Zeek would be monitoring.

### Kill a TCP connection with forged RSTs

Tear down a specific flow by its 4-tuple and sequence numbers.

```
rst 198.51.100.10 443 1000 198.51.100.20 50000 2000
```

Outcome: Sends forged RST packets for the connection "from 198.51.100.10:443 (seq 1000) to 198.51.100.20:50000 (seq 2000)", prompting both endpoints to close — the same primitive a Zeek script triggers; options include `-i` to pick the egress interface and `-r`/`-n` for redundancy and repeat counts.

*No corpus evidence — derived from upstream documentation.*
