---
name: "sniffer"
categories:
  - blackarch-networking
version: "4.688854e"
homepage: "https://github.com/julioreynaga/sniffer"
packaged-by: [blackarch, aur]
blackarch-builddate: "2017-08-19"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "julioreynaga/sniffer"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# sniffer

> Offline pcap parser set that recovers TCP streams, SMTP emails and HTTP cookies from packet traces.

## What it's for

Four small C parsers that read a tcpdump-produced pcap file and extract what passed on the wire: per-packet header summaries, reassembled TCP connection payloads, complete SMTP email messages, and HTTP cookie name/value pairs. They work entirely offline on a saved capture — no live capture and no remote access. Suited to evidence extraction from a trace collected elsewhere. The upstream repository has disappeared; the packaged binaries and the archived README are the remaining reference.

## Typical use cases

### Summarize packets and headers in a trace

```
packet-parser capture.pcap
```

Outcome: prints each packet's type (TCP/UDP/other), source and destination MAC, IP addresses and ports, checksum and payload size, ending with totals of TCP, UDP and other packets processed.

### Reassemble TCP streams per connection

```
tcp-parser capture.pcap
```

Outcome: writes three files per connection in the current directory — `N.meta` (endpoints, ports, per-direction packet/byte counts, duplicates, close status), `N.initiator` and `N.responder` holding the acknowledged payload bytes each side sent.

### Recover SMTP email messages

```
email-parser capture.pcap
```

Outcome: writes one `N.mail` file per message sent to an SMTP server in the trace, containing sender/receiver IPs, whether the server accepted it, and the full message headers and body.

### Extract HTTP cookies

```
cookie-parser capture.pcap
```

Outcome: writes one `N.cookie` file per HTTP connection containing every cookie name/value pair observed in that connection, ready for session-analysis follow-up.

*No corpus evidence — derived from upstream documentation.*
