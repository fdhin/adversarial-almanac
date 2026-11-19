---
name: "packetq"
categories:
  - blackarch-networking
version: "311.9ebf537"
homepage: "https://github.com/DNS-OARC/PacketQ"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-08"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "DNS-OARC/PacketQ"
github-stars: 395
github-pushed: "2026-02-04"
github-archived: true
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# packetq

> Run SQL queries directly on PCAP files — built for DNS traffic analysis, with JSON/CSV/XML output and a small web UI.

## What it's for

PacketQ (maintained by DNS-OARC; originally DNS2db) treats a PCAP file as a database: it decodes packets (DNS and ICMP built in, DNS is the strong suit) and lets you SELECT, filter, group, sort and count across them with plain SQL, including aggregation functions and per-country/AS enrichment of addresses. Results come out as JSON by default, or formatted/compact CSV and XML. Use it for passive DNS traffic accounting from captures — top queriers, top domains, response codes — without writing a parser. It is read-only against captures (plain or gzipped), does not write PCAPs, and the upstream repository is archived, so it is a stable historical tool rather than an actively developed one.

## Typical use cases

### Extract the first DNS packets from a capture

Peek at decoded records with a trivial query.

```
packetq -s "select * from dns limit 10" capture.pcap
```

Outcome: prints the first 10 DNS packets as JSON, each decoded into columns such as `src_addr`, `dst_addr`, `qname`, `qtype` and `rcode`.

### Rank the most-queried domains

Aggregate the whole capture with group by.

```
packetq -s "select qname, count(*) as count from dns group by qname order by count desc limit 10" capture.pcap
```

Outcome: a top-10 table of query names by occurrence count, emitted as JSON — a one-shot passive-DNS popularity report over the capture.

### Rank the most active client addresses

Find who generated the traffic.

```
packetq -s "select src_addr, count(*) as count from dns group by src_addr order by count desc limit 15" capture.pcap
```

Outcome: the 15 busiest resolver client IPs with their query counts, useful for spotting a single host hammering the resolver.

### Serve PCAPs through the built-in web server

Expose captures for interactive browser queries.

```
packetq -d -p8080 -w html/ -r pcap/
```

Outcome: packetq daemonizes (`-d`) and listens on port 8080, serving its browser GUI from `html/` and making the PCAP files under `pcap/` queryable through the web interface without copying files around.

*No corpus evidence — derived from upstream documentation.*
