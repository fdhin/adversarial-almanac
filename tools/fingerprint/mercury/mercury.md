---
name: "mercury"
categories:
  - blackarch-fingerprint
version: "2.16.1.r0.g0d7fbce"
homepage: "https://github.com/cisco/mercury"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-05-02"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "cisco/mercury"
github-stars: 522
github-pushed: "2026-10-09"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1792
gh-activity-checked: "2026-11-09"
---

# mercury

> High-performance passive network metadata capture and fingerprinting (TLS, DTLS, SSH, HTTP, TCP) as JSON or PCAP.

## What it's for

Cisco's mercury reads network packets, extracts metadata of interest, and
writes it out as JSON fingerprint records — or saves the matching packets to a
PCAP file. It produces normalized fingerprint strings for TLS, DTLS, SSH, HTTP
and TCP, and can optionally annotate each fingerprint with process
identification (the `analysis` object) using its fingerprint/resource files.
On Linux it uses the kernel's AF_PACKET TPACKETv3 zero-copy path with
configurable ring buffers and worker threads, scaling to high data rates
(40 Gbps on server-class hardware); on macOS it captures via libpcap. It is a
sensor, not an active scanner: it observes traffic (live or from a capture
file) and never sends probes. A portable Python implementation, pmercury, is
included in the same repository.

## Typical use cases

### Watch live traffic and print fingerprints

```
mercury -c auto
```

Outcome: auto-detects the active capture interface and prints JSON records
with a `fingerprint` object per observed TLS/SSH/HTTP/TCP handshake to stdout
— an immediate view of what clients and servers are on the segment.

### Capture to a PCAP while fingerprinting at full speed

```
mercury -c eth0 -w capture.pcap -t cpu
```

Outcome: captures from eth0 with one worker thread per CPU, writing the raw
matching packets to `capture.pcap` for later analysis or replay.

### Extract fingerprints from an existing capture

```
mercury -r capture.pcap -f fingerprints.json
```

Outcome: reads the packet capture file and writes the normalized fingerprint
JSON records to `fingerprints.json` without touching the network.

### Capture with fingerprint analysis enabled

```
mercury -c eth0 -t cpu -f fingerprints.json -a
```

Outcome: as above but each fingerprint is additionally annotated in the
`analysis` object (process/client identification based on the fingerprint and
destination context) — useful to spot known malware toolkits in passive
traffic.

*No corpus evidence — derived from upstream documentation.*
