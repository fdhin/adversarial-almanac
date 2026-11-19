---
name: "silk"
categories:
  - blackarch-networking
version: "3.24.2"
homepage: "https://tools.netsa.cert.org/silk/download.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-06-14"
build-system: autotools
license: GPL-2.0-only
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# silk

> CERT NetSA's flow-analysis suite: filter, cut and aggregate very large NetFlow/IPFIX repositories.

## What it's for

SiLK (System for Internet-Level Knowledge) is the CERT NetSA toolchain for network flow data at backbone scale. A packing system converts NetFlow v5/v9 and IPFIX into space-efficient binary repositories; the analysis suite — rwfilter, rwcut, rwstats, rwcount and dozens more — queries those repositories, with every tool reading and writing the common `.rw` record format and composing through pipes. It is built for analysts querying weeks of historical traffic on enterprise or ISP borders, not for packet-level inspection: it sees flows (who talked to whom, when, how much), never payloads. Source releases are distributed from tools.netsa.cert.org — the project has no canonical GitHub repository.

## Typical use cases

### Select flows into a result file

Pull the records you care about out of the repository.

```
rwfilter --start-date=2026/11/08:14 --end-date=2026/11/08:15 --type=all \
    --any-address=198.51.100.10 --pass-destination=flows.rw
```

Outcome: scans the repository files covering the time window, writes matching records to flows.rw and prints file-processing statistics to stderr; `--any-address` matches either endpoint, and the destination also accepts `stdout` or `-` to stream records instead of writing a file.

### Inspect the selected records

Display flows as a readable table.

```
rwcut --fields=sip,dip,sport,dport,protocol,stime --num-recs=10 flows.rw
```

Outcome: prints the first ten records as a pipe-delimited table (sIP|dIP|sPort|dPort|pro|sTime); `--help-fields` lists every available column name.

### Pipe a filter straight into a view

Compose tools without intermediate files.

```
rwfilter --type=all --any-address=198.51.100.10 --pass-destination=stdout | \
    rwcut --fields=sip,dip,bytes,packets --num-recs=20
```

Outcome: matching flows stream directly from rwfilter into rwcut and print — the standard SiLK idiom for building a query incrementally one stage at a time.

### Rank top talkers

Aggregate by key and sort by volume.

```
rwstats --fields=sip --values=bytes --count=10 --top flows.rw
```

Outcome: sums bytes per source IP and prints the ten largest with counts and percentages; `--bottom` reverses the order and `--threshold`/`--percentage` select by magnitude instead of rank.

### Chart traffic volume over time

Bin the selection into time slots.

```
rwcount --bin-size=3600 --skip-zeroes flows.rw
```

Outcome: one row per hour-sized bin with date/time, records, bytes and packets — a load profile of the selection; `--start-time`/`--end-time` bound the series.

### Inventory the flow repository

See what data the deployment actually holds.

```
rwsiteinfo --fields=sensor,class,type
```

Outcome: lists the sensors, classes and flow types present under the configured data root — the reference for valid `--sensor`/`--class`/`--type` values when writing rwfilter queries.

*No corpus evidence — derived from upstream documentation.*
