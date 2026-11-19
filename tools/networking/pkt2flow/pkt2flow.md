---
name: "pkt2flow"
categories:
  - blackarch-networking
version: "69.868a2e8"
homepage: "https://github.com/caesar0301/pkt2flow"
packaged-by: [blackarch]
blackarch-builddate: "2019-03-24"
build-system: other
license: MIT
meta-checked: "2026-11-09"
github-repo: "caesar0301/pkt2flow"
github-stars: 177
github-pushed: "2025-08-12"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pkt2flow

> Splits a capture into one pcap file per flow, keyed by the essential 4-tuple.

## What it's for

pkt2flow classifies packets from a capture file into flows using only the essential 4-tuple (src_ip, dst_ip, src_port, dst_port) and writes each flow as its own pcap, named with the tuple and the timestamp of the flow's first packet. Unlike tcpflow it performs no payload reassembly and no content processing — pure flow separation, which is exactly what deep-packet-inspection experiments, flow-classification research, and per-conversation analysis need. By default only valid TCP flows (those seen starting with a SYN) are dumped; flags extend coverage to UDP flows, invalid TCP flows, and other IP protocols. It reads capture files only — there is no live-capture mode.

## Typical use cases

### Split all TCP and UDP flows

One file per conversation for per-flow analysis.

```
pkt2flow -u -o output_flows/ input.pcap
```

Outcome: writes one pcap per TCP and UDP flow into `output_flows/`, each named with its 4-tuple and first-packet timestamp; feed the individual flow files to any per-flow analysis.

### Split only valid TCP flows

Separate cleanly established conversations.

```
pkt2flow capture.pcap
```

Outcome: dumps TCP flows that began with a SYN handshake into the working directory, skipping mid-stream or incomplete flows.

### Dump everything, including odd traffic

Keep non-TCP/non-UDP and non-SYN flows too.

```
pkt2flow -u -v -x -o all_flows/ capture.pcap
```

Outcome: additionally writes UDP (`-u`), TCP flows lacking a SYN (`-v`), and other IP protocols such as ICMP (`-x`), so no conversation in the capture is silently dropped.

*No corpus evidence — derived from upstream documentation.*
