---
name: "tcpflow"
categories:
  - forensics
  - sniffing-spoofing
version: "1.6.1-3"
homepage: "https://github.com/simsong/tcpflow"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-05"
build-system: autotools
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "simsong/tcpflow"
github-stars: 1779
github-pushed: "2026-11-03"
github-archived: false
github-issues: 78
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# tcpflow

> TCP flow recorder: reassembles each TCP connection into its own file.

## What it's for

tcpflow captures TCP connections live or from a saved pcap and reconstructs the actual byte stream of each connection — handling sequence numbers, retransmissions, and out-of-order delivery — writing one file per direction named `sourceip.sourceport-destip.destport`. Where a packet analyzer such as tcpdump shows packet-by-packet summaries, tcpflow is for reading whole sessions: recovering web pages, credentials, or transferred files from captured HTTP, FTP, or SMTP traffic. It does not decode non-TCP protocols and does not understand IP fragments; flows containing fragments are not recorded correctly.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Reconstruct every flow from a saved capture

Turn a packet capture into per-connection data files.

```
tcpflow -a -o outdir -Fk -r packets.pcap
```

Outcome: reads packets.pcap, enables all processing (`-a`), and writes each flow under outdir/ binned into subdirectories of 1000 connections (`-Fk`); each output file's name encodes source and destination IP and port.

### Capture flows live with a BPF filter

Record sessions as they happen.

```
tcpflow -i eth0 port 80
```

Outcome: sniffs eth0 and starts a new output file per matching TCP connection, named like `198.51.100.10.51024-198.51.100.20.00080` in the current directory; stop with Ctrl-C and inspect the files with less or grep.

### Print reassembled streams to the console

Watch session data without writing files.

```
tcpflow -c -r packets.pcap port 80
```

Outcome: prints flow contents to stdout as they are reassembled; `-C` does the same without the per-flow source/destination banners, which keeps the output clean for piping into grep.

### Write a DFXML inventory of all flows

Get a machine-readable report of the capture.

```
tcpflow -a -r packets.pcap -X report.xml
```

Outcome: extracts all flows and additionally writes report.xml, an XML document listing every flow with addresses, ports, packet and byte counts and timestamps, suitable for scripted triage.
