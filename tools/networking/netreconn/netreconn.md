---
name: "netreconn"
categories:
  - blackarch-networking
version: "1.78"
homepage: "http://packetstormsecurity.com/files/86076/NetReconn-Scanning-Tool-Collection-1.76.html"
packaged-by: [blackarch]
blackarch-builddate: "2019-12-25"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Seabreg/netreconn"
github-stars: 0
github-pushed: "2019-09-08"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netreconn

> One small C binary, five modes: fast port/host scanning, passive host discovery, and tiny ARP/TCP sniffers.

## What it's for

NetReconn ("network-reconnoiter", by Jason R. Fink; released via PacketStorm as a scanning-tool collection) is a deliberately minimal recon toolkit for quick network triage. The single binary offers `scan` for fast non-blocking connect/select port checks with built-in subnet ranges and fine-grained timeouts, `scan6` for experimental IPv6 scanning, `passive` to infer hosts and likely-open ports purely from captured traffic, plus `tcpdump` and `arpsniff` modes that are tiny libpcap-based sniffers with optional ASCII payload decoding. Its only dependency is libpcap, and its own README positions it as a complement to — not a replacement for — nmap and tcpdump: smaller and faster to deploy where the big tools are overkill. The package installs the binary as `netreconn`; netrecon(1) documents every mode.

GitHub: third-party mirror of the Packet Storm NetReconn distribution (Seabreg/netreconn).

## Typical use cases

### Check a single port on a single host

```
netreconn scan --port 22 target
```

Outcome: performs a fast connect-check of TCP/22 on target and reports the answer — the quick "is SSH there" probe without launching a full scanner.

### Sweep a port range across a subnet

```
netreconn scan --port 22-80 -V 198.51.100.1-255
```

Outcome: scans ports 22 through 80 across the address range using the tool's native x.x.x.x-X subnet syntax (no wrapper loops needed), with `-V` printing each result as it lands; `--conn` switches to full connects and `--extra` reaches ports above 1024.

### Discover hosts and ports without sending anything

```
netreconn passive --extra --polls 32 --if eth0
```

Outcome: captures on eth0 for 32 polls and infers live hosts and possibly-open ports from the observed traffic, flagging a port once it crosses the detection threshold — reconnaissance that touches the target with zero packets of its own.

### Monitor ARP traffic with decoding

```
netreconn arpsniff --if eth1 --decode
```

Outcome: watches ARP packets on eth1 and prints them, `--decode` rendering payload bytes in ASCII — a dependency-light ARP monitor for spotting spoofing or unexpected speakers on a segment.

*No corpus evidence — derived from upstream documentation.*
