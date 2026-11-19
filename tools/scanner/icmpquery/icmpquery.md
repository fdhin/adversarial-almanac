---
name: "icmpquery"
categories:
  - blackarch-scanner
version: "1.0"
homepage: "http://www.angio.net/security/"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/icmpquery"
github-stars: 0
github-pushed: "2019-03-18"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# icmpquery

> Classic ICMP information queries (David G. Andersen, angio.net): ask hosts for their subnet address mask or their clock via timestamp requests — reconnaissance older than nmap's OS detection.

*No corpus evidence — derived from upstream documentation (icmpquery.c usage banner read from the packaged source).*

## What it's for

icmpquery sends the two ICMP query message types that predate modern reconnaissance: ICMP Address Mask requests (`-m`), which some legacy hosts and misconfigured routers still answer with their subnet mask, and ICMP Timestamp requests (`-t`, the default), which report the remote system's clock relative to yours. The mask reply leaks topology information; the timestamp reply leaks clock skew that can hint at system type and uptime — accurate only when the local clock is correct, since the reply is relative. Firewalls routinely drop both query types today, so a response at all is itself a datapoint about permissive filtering. It is a single-purpose C tool: one query type per run, one or more targets, no scanning, no exploitation.

## Typical use cases

### Ask a host for its subnet mask

```
icmpquery -m 198.51.100.10
```

Outcome: Sends an ICMP address mask request and prints the reply (`host : mask 255.255.255.0` style) — a leaked mask maps network topology and hints at legacy or loosely filtered hosts; no output means the query was dropped or ignored.

### Compare a remote system's clock

```
icmpquery -t 198.51.100.10
```

Outcome: Sends an ICMP timestamp request and prints the returned time offset — measurable clock skew feeds device fingerprinting and can flag embedded/legacy systems (keep the local clock synced or the offset is meaningless).

### Sweep a subnet in broadcast mode

```
icmpquery -B -m 198.51.100.255 -T 10
```

Outcome: Broadcast mode (`-B`) sends one query and collects every response for up to 10 seconds (`-T`, default 5), listing all answering hosts on the segment in one shot; `-d` spaces packets out in microseconds on slower links.
