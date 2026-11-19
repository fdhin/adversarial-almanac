---
name: "argus-clients"
categories:
  - blackarch-networking
version: "3.0.8.3"
homepage: "http://qosient.com/argus/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-05-04"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "openargus/clients"
github-stars: 23
github-pushed: "2026-11-03"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 117
gh-activity-checked: "2026-11-09"
---

# argus-clients

> The ra* analysis suite for Argus flow data — filter, aggregate, print, graph and store network transaction audits.

## What it's for

argus-clients (QoSient, LLC) is the reading half of the Argus system — [[argus]] generates the flow data, this package ships the programs that make it useful: `ra` reads, filters and prints records; `racluster` aggregates them by flow keys; `ratop` shows a live top-style view; `rafilteraddr` selects records against address lists; and the suite also includes `rasort`, `rasplit`, `rahisto`, `ragraph`, `ranonymize`, `rapolicy`, `rasql` (MySQL) and more. All of them read Argus data from files, stdin or a live Argus server, share the common `ra(1)` options, and take their defaults from a `rarc(5)` configuration file.

## Typical use cases

### Read and filter a recorded audit trail

```
ra -r argus.out -c , -s saddr daddr proto sport dport pkts bytes -- tcp
```

Outcome: prints the TCP flows from the audit file as comma-separated columns (`-s` selects the fields, `-c` the delimiter) with the libpcap-style filter after `--`; the tabular stdout output can be piped straight into further processing.

### Attach to a live Argus server

```
ra -S 127.0.0.1:561 -s saddr daddr sport dport bytes -- host 198.51.100.10
```

Outcome: streams matching flow records in real time from a sensor running with `-P 561` (561 is the default port) — an instant traffic window without touching files.

### Aggregate into per-conversation summaries

```
racluster -r argus.out -m saddr daddr proto -s saddr daddr proto pkts bytes
```

Outcome: collapses the audit into one record per src/dst/protocol tuple (`-m` sets the aggregation objects; `sport`, `dport`, `matrix` and MAC/VLAN keys also work) with summed packets and bytes — the base data for top-talker analysis.

### Watch live traffic as a top-style dashboard

```
ratop -S 127.0.0.1:561
```

Outcome: an interactive, periodically refreshing view of the busiest flows on the sensor, sortable like `top`.

### Filter records into a sub-audit file

```
rafilteraddr -r argus.out -f addresses.txt -w dns.argus -- udp port 53
```

Outcome: writes only the records whose addresses appear in `addresses.txt` (one per line) and that match the filter expression into `dns.argus` in argus(5) format, ready to hand to any other ra* tool.

*No corpus evidence — derived from upstream documentation.*
