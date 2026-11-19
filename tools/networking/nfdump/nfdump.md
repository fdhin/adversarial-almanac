---
name: "nfdump"
categories:
  - blackarch-networking
version: "1.6.23"
homepage: "https://github.com/phaag/nfdump"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2021-06-22"
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
github-repo: "phaag/nfdump"
github-stars: 925
github-pushed: "2026-10-16"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 190
gh-activity-checked: "2026-11-09"
---

# nfdump

> The standard NetFlow/IPFIX/sFlow stack: collect flows with nfcapd, then filter, aggregate and report them with nfdump.

## What it's for

nfdump (by Peter Haag) is a suite of tools for collecting and analyzing flow data. `nfcapd` and `sfcapd` receive NetFlow (v1/v5/v7/v9), IPFIX and sFlow exports from routers and switches and store them as compact binary files rotated every few minutes; the `nfdump` reader then selects, filters (a tcpdump-like syntax extended for flow records), aggregates and prints them as text, CSV or JSON. Use it to answer who-talked-to-whom traffic questions from flow records instead of full packet captures, for live monitoring or retrospective analysis. It does not capture packets itself (the optional `nfpcapd` converts interfaces or pcaps to flows), and companions cover anonymization (`nfanon`), retention (`nfexpire`), replay to another collector (`nfreplay`) and geo/Tor enrichment.

## Typical use cases

### Collect NetFlow from an exporter

Start a collector daemon for one source router.

```
nfcapd -D -S 2 -w /flow_base_dir/router1 -p 23456
```

Outcome: nfcapd daemonizes (`-D`, syslog level `-S 2`), binds UDP port 23456, and writes incoming flow records to time-sliced files named `nfcapd.YYYYMMDDhhmm` under `/flow_base_dir/router1` — 288 files per day at the default 5-minute rotation.

### Read collected flows back

Summarize one collection interval.

```
nfdump -r /flow_base_dir/router1/nfcapd.202501011200
```

Outcome: prints per-protocol flow/packet/byte statistics followed by the individual flow lines (source, destination, ports, duration, packets, bytes) for that 5-minute slot.

### Filter and aggregate flows

Select traffic of interest and group it.

```
nfdump -r /flow_base_dir/router1/nfcapd.202501011200 'src ip 198.51.100.10 and dst port 443' -A srcip,dstip
```

Outcome: only flows from `198.51.100.10` to port 443 are read, aggregated per source/destination IP pair (`-A`), and printed with combined counters — a quick who-is-talking-to-whom table.

### File multiple exporters separately

Run one collector that splits sources into per-router directories.

```
nfcapd -D -S 2 -n router1,198.51.100.20,/flow_base_dir/router1 -n router2,198.51.100.30,/flow_base_dir/router2 -p 23456
```

Outcome: a single nfcapd on port 23456 identifies each exporter by IP and stores its flows in its own directory, keeping sources separable for per-router analysis.

### Convert old flow files to the current format

Modernize legacy 1.6.x files so every tool can read them.

```
nfdump -r old-nfcapd-file -y -w new-nfcapd-file
```

Outcome: reads the legacy record format and rewrites it with `-w` in the current format (flagged `-y`); only `nfdump` itself can read the old files, all other suite tools require the converted form.

*No corpus evidence — derived from upstream documentation.*
