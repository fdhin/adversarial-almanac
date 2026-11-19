---
name: "pmacct"
categories:
  - blackarch-networking
version: "v1.7.6.r1545.g5824ef3"
homepage: "https://github.com/pmacct/pmacct"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-04-06"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "pmacct/pmacct"
github-stars: 1257
github-pushed: "2026-11-04"
github-archived: false
github-issues: 47
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 187
gh-activity-checked: "2026-11-09"
---

# pmacct

> Passive network accounting suite: libpcap accounting plus NetFlow/IPFIX, sFlow, NFLOG, BGP, BMP and telemetry collectors with SQL/AVRO/JSON backends.

## What it's for

pmacct is a suite of daemons and a query client for passive network measurement, maintained by Paolo Lucente. pmacctd accounts traffic it sniffs via libpcap; nfacctd, sfacctd and uacctd collect NetFlow/IPFIX, sFlow, and Linux NFLOG input respectively; pmbgpd, pmbmpd and pmtelemetryd passively collect BGP, BMP and streaming telemetry. Each daemon aggregates traffic by configurable keys (hosts, ports, AS numbers, ...) and feeds pluggable backends — in-memory caches, print/CSV/JSON output, SQL databases, or re-export toward third-party collectors via NetFlow/IPFIX/sFlow. The `pmacct` client queries a running daemon's memory tables, filtering and formatting (CSV, JSON) the results. It is monitoring and analytics only — no packet injection or active scanning.

## Typical use cases

### Watch a live traffic matrix on an interface

Top-style visibility without any config file.

```
pmacctd -P print -r 30 -i eth0 -c src_host,dst_host
```

Outcome: aggregates sniffed traffic by source/destination host and refreshes the counter table on the console every 30 seconds; stop with Ctrl-C, nothing is persisted.

### Persist accounting to MySQL with a capture filter

Record only the traffic you care about into SQL.

```
pmacctd -D -c src_host,dst_host -i eth0 -P mysql src net 198.51.100.0/24
```

Outcome: daemonizes (`-D`), sniffs eth0 through the tcpdump-style BPF filter, and writes per src_host/dst_host aggregates into MySQL tables for later SQL analysis.

### Collect NetFlow/IPFIX from routers

Terminate exporter traffic from one or many routers.

```
nfacctd -D -c sum_host -P mysql -l 5678
```

Outcome: daemonizes and listens on port 5678 for NetFlow v5/v9 or IPFIX datagrams, aggregating inbound+outbound bytes per host (sum_host) into MySQL.

### Account via NFLOG instead of a capture interface

Count exactly the packets iptables selects.

```
uacctd -f uacctd.conf
```

Outcome: reads packets from an NFLOG multicast group (fed by the iptables NFLOG target) rather than sniffing an interface, applying the same aggregation and plugin backends — Linux only.

### Query a running daemon's counters

Inspect what a daemon has collected so far.

```
pmacct -s -O json | jq
```

Outcome: connects to a running daemon's memory cache and snapshots its counters as JSON (`-O csv` for CSV, or plain formatted output), ready for scripting.

### Match counters for specific hosts

Pull just the rows you need from a daemon's cache.

```
pmacct -c dst_host -N 198.51.100.20 -p /tmp/pipe.eth0
```

Outcome: queries the daemon whose cache socket is /tmp/pipe.eth0 and prints only aggregates whose destination host is 198.51.100.20; `-N` also accepts comma/semicolon-separated lists and `file:` references.

*No corpus evidence — derived from upstream documentation.*
