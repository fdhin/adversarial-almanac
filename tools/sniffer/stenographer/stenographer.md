---
name: "stenographer"
categories:
  - blackarch-sniffer
version: "486.355604b"
homepage: "https://github.com/google/stenographer"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-27"
build-system: go
license: APACHE
meta-checked: "2026-11-09"
github-repo: "google/stenographer"
github-stars: 1795
github-pushed: "2021-07-26"
github-archived: true
github-issues: 50
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stenographer

> Full-packet-capture daemon that spools all packets to disk at high speed for later selective retrieval.

## What it's for

Stenographer (Google, archived 2021) is packet-capture infrastructure for intrusion detection and incident response: the stenotype daemon writes every packet to disk as fast as the hardware allows (around 10 Gbps on multi-core, multi-disk machines), manages disk usage by deleting the oldest packets when limits are hit, and exposes a simple BPF-subset query language so an analyst can pull back only the packets matching a filter. It deliberately does no stream reassembly or deep per-packet processing, and it is designed for readback of a small fraction (<1%) of what it stores — it is a recorder, not an analyzer. The upstream repository is archived, so the tool works but no longer sees development.

## Typical use cases

### Read back packets for a host and port

Pull one conversation out of the spool via the stenoread front end.

```
stenoread 'host 198.51.100.10 and port 6543' 'tcp[tcpflags] & tcp-push != 0'
```

Outcome: stenographer returns the matching stored packets and stenoread runs them through tcpdump with the extra filter, printing the PSH-flagged segments of that conversation; the second argument (any tcpdump options) is optional.

### Export a query's packets to a pcap for Wireshark

Save a slice of the spool for GUI analysis.

```
stenoread 'net 198.51.100.0/24' -w /tmp/evidence.pcap
```

Outcome: writes every stored packet from that subnet to /tmp/evidence.pcap, ready to open in Wireshark.

### Query a time window

Combine address filters with stenographer's time primitives.

```
stenoread 'port 8765 and after 3h ago'
```

Outcome: returns packets on that port captured within the last three hours; before/after also accept absolute timestamps such as 2012-11-03T11:05:00Z or timezone-offset forms.

### Format output with tcpdump options

Apply tcpdump display options to the retrieved packets.

```
stenoread 'port 8765' -n -e
```

Outcome: prints matching packets without DNS resolution (-n) and with link-level headers (-e); every argument after the query passes to tcpdump verbatim.

*No corpus evidence — derived from upstream documentation.*
