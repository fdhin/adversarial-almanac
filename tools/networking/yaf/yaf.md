---
name: "yaf"
categories:
  - blackarch-networking
version: "2.12.2"
homepage: "https://tools.netsa.cert.org/yaf/download.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2021-12-25"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "britram/cert-netsa-yaf"
github-stars: 3
github-pushed: "2019-01-28"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# yaf

> Yet Another Flowmeter — CERT NetSA's tool that turns packets (pcap or live capture) into IPFIX flow records.

## What it's for

yaf is the flow generation stage of CERT NetSA's network-survey architecture: it reads packets from a pcap file or a live interface and exports bidirectional flow records in IPFIX format, which downstream tools (an IPFIX collector, or the SiLK suite) analyze. Beyond basic 5-tuple flows it can label each flow with the application protocol, retain truncated payloads, and perform deep packet inspection for selected protocols via DPI rules, plus DHCP fingerprinting through a plugin. Live capture supports plain libpcap as well as specialized hardware (Endace DAG, Napatech, Netronome). It produces flow data, not alerts — detection and reporting happen in whatever consumes its output. Its sibling utility yafscii renders `.yaf` flow files as readable ASCII. Note: yaf is distributed from tools.netsa.cert.org and has no canonical GitHub repository (the flowmeter shares its name with an unrelated PHP framework).

GitHub: third-party git mirror of the CERT NetSA release tarballs (britram/cert-netsa-yaf); the canonical distribution remains tools.netsa.cert.org.

## Typical use cases

### Convert a pcap into an IPFIX flow file

Get flow records for offline analysis from a packet capture.

```
yaf --in packets.pcap --out flows.yaf
```

Outcome: Writes flows.yaf, a YAF/IPFIX file containing one bidirectional flow record per flow observed in the capture; read it back as text with yafscii. The same conversion also works via stdin/stdout: `yaf < packets.pcap > flows.yaf`.

### Capture live traffic into hourly rotated files

Monitor an interface continuously and keep bounded flow files.

```
yaf --live pcap --in eth0 --out eth0_capture --rotate 3600
```

Outcome: Captures from eth0 and writes flow files named after the output prefix, rotated every 3600 seconds, so disk usage stays bounded during long captures.

### Stream flows to a collector over TCP

Feed a central IPFIX collector instead of writing files.

```
yaf --live pcap --in eth0 --out my-collector.example.com --ipfix tcp
```

Outcome: Exports IPFIX records over TCP to the collector at my-collector.example.com as flows are generated (`--ipfix udp` switches the transport; `--ipfix-port` selects a non-default port).

### Restrict flow generation with a BPF filter

Only turn a subset of traffic into flows.

```
yaf --live pcap --in eth0 --out /tmp/dnsflows --rotate 120 --filter="udp port 53"
```

Outcome: Only DNS packets pass the BPF filter, so the rotated output files contain exclusively DNS flows.

### Add application labeling and DPI to flows

Enrich records with protocol identification and payload inspection.

```
yaf --in packets.pcap --out flows.yaf --applabel --max-payload=400 --dpi --dpi-select="80 143 53"
```

Outcome: Flow records carry application labels, up to 400 bytes of retained payload, and DPI results for HTTP, IMAP and DNS — enough metadata to profile what the flows actually contain.

*No corpus evidence — derived from upstream documentation.*
