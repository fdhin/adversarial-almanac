---
name: "tcpdstat"
categories:
  - blackarch-networking
version: "4.be5bd28"
homepage: "https://github.com/netik/tcpdstat"
packaged-by: [blackarch]
blackarch-builddate: "2018-04-21"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "netik/tcpdstat"
github-stars: 16
github-pushed: "2010-06-14"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tcpdstat

> One-pass protocol and size statistics for pcap files, before loading them into anything heavier.

## What it's for

tcpdstat reads a libpcap capture file and prints a summary: the trace's time span, total packets and bytes, the breakdown by protocol (IPv4/IPv6, TCP, UDP, ICMP and recognized port-based services such as http, ssh, domain), plus per-flow accounting including the top bandwidth consumers. It answers "what is in this trace" in a single fast pass so you can decide what to extract next. It analyzes a saved file offline; it does not capture traffic itself. This GitHub tree is Twitter's fork of tcpdstat-uw with extra protocol recognizers, originally by Kenjiro Cho at Sony CSL.

## Typical use cases

### Get the protocol breakdown of a capture

```
tcpdstat capture.pcap
```

Outcome: prints duration, packet and byte totals, the percentage split across protocols and recognized services, and the top bandwidth flows — a one-screen profile of what the trace contains.

### Skip the per-flow detail

```
tcpdstat -n capture.pcap
```

Outcome: the same protocol summary without flow accounting — faster pass over large traces when only the protocol mix matters.

### Profile only the start of a huge capture

```
tcpdstat -c 1000 capture.pcap
```

Outcome: statistics computed over only the first 1000 packets — a quick sample of a multi-gigabyte capture without waiting for a full pass.

*No corpus evidence — derived from upstream documentation.*
