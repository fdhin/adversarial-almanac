---
name: "flashlight"
categories:
  - blackarch-recon
version: "109.90d1dc5"
homepage: "https://github.com/galkan/flashlight"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "galkan/flashlight"
github-stars: 195
github-pushed: "2019-06-23"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# flashlight

> Python 2-era recon automator that wraps nmap, tshark and phantomjs into passive, active, screenshot and pcap-filtering scans of a target network.

## What it's for

Flashlight (Fener, by galkan) automates the discovery phase of a network penetration test. It runs four scan types from one interface: a passive scan that sniffs an interface into PCAP files without sending packets, an active scan that drives nmap from a YAML config (ports, UDP ranges, NSE scripts), a screenshot scan that captures web applications on the configured ports via phantomjs/rasterize.js, and a filter mode that post-processes captured PCAP files (Windows hosts, top DNS requests). Projects get separate workspaces with `nmap/`, `pcap/` and `screen/` output subdirectories. It is a 2015-era Python 2 tool (BlackArch wraps it as `flashlight`) and depends on nmap, tshark/tcpdump and dsniff being installed; it does not exploit anything — it is purely information gathering.

## Typical use cases

### Passive sniff of a segment

Capture traffic on an interface without touching the wire.

```
flashlight -s passive -p passive-proj -i eth0 -o ./flashlight-out -l ./scan.log -v
```

Outcome: sniffs on `eth0` for the default 15 seconds (`-k` changes the timeout, e.g. `-k 50`) and writes a timestamped PCAP under `./flashlight-out/output/passive-proj/pcap/` plus a log file — a silent record of who is talking on the segment.

### Passive capture with ARP-spoofed MITM position

Collect traffic that normally bypasses your port.

```
flashlight -s passive -p mitm-proj -i eth0 -g 198.51.100.1 -m -k 50 -v
```

Outcome: ARP-spoofs the gateway given with `-g` (`-m`/`--mim`), captures 50 seconds of intercepted traffic into the project's `pcap/` directory — HTTP traffic and Basic-Auth headers captured here can be analyzed directly.

### Active nmap sweep of a subnet

Run the configured port and script scans against a network range.

```
flashlight -p active-proj -s active -d 198.51.100.0/24 -a -t 30 -v
```

Outcome: ping-scans for live hosts (`-a`), then runs the nmap port/script scans defined in `flashlight.yaml` (TCP/UDP port lists, NSE scripts like `http-enum`), writing Normal/XML/Grepable outputs for OS, ping, port and script scans under `output/active-proj/nmap/`; add `-n` to apply nmap timing/parallelism optimizations for large ranges.

### Screenshot every web app in a range

Capture visual evidence of web services.

```
flashlight -p screen-proj -s screen -d 198.51.100.0/24 -r /usr/local/rasterize.js -t 10 -v
```

Outcome: probes the `screen_ports` from the config (default 80, 443, 8080, 8443) and saves a screenshot of every responsive web application under `output/screen-proj/screen/` for offline review, using 10 threads.

### Filter an existing PCAP

Mine an already-captured PCAP for host intelligence.

```
flashlight -p filter-proj -s filter -f ./capture.pcap -v
```

Outcome: analyzes the PCAP and writes breakdowns (Windows hosts, top 10 DNS requests, and similar) into a `filter/` subdirectory of the output tree.

*No corpus evidence — derived from upstream documentation.*
