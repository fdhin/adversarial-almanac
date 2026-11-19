---
name: "pcapxray"
categories:
  - blackarch-forensic
version: "v5.2.r1.ga454d12"
homepage: "https://github.com/Srinivas11789/PcapXray"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-02"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Srinivas11789/PcapXray"
github-stars: 1892
github-pushed: "2026-05-01"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 53
gh-activity-checked: "2026-11-09"
---

# pcapxray

> Visualize a packet capture as an annotated network diagram — devices, flows, Tor sessions and flagged malicious traffic — from a desktop GUI.

## What it's for

PcapXray turns a `.pcap`/`.pcapng` file (or a live interface capture) into a network diagram so an investigator can see at a glance which devices talked, which servers were contacted, and which sessions look malicious or Tor-routed. It enriches the graph with device identification (OUI vendor lookup, hostname resolution, whois for public addresses), and extracts per-session payloads, DNS queries and TLS records into reports. Output comes in three forms: a static Graphviz-rendered PNG of the LAN topology, an interactive pyvis HTML graph for the browser, and a matplotlib graph panel. It is a GUI application (Tkinter), not a batch CLI — analysis is driven by picking a file, choosing one of four parsing engines (`auto`, `dpkt`, `scapy`, `pyshark`) and clicking through the results, with an SQLite store caching sessions so a capture can be reloaded without re-analysis.

## Typical use cases

### Analyze a capture file and render the network diagram

Launch the application against a stored capture.

```
python3 Source/main.py
```

Outcome: opens the GUI; browse to a `.pcap`/`.pcapng` file, choose an output directory and engine, then **Analyze!** parses the sessions into the SQLite-backed store and **Visualize!** writes the annotated topology PNG plus device/traffic report files; **Interactive Graph** opens the pyvis HTML view and **Graph Panel** the matplotlib window. Malicious and Tor sessions are highlighted on the diagram.

### Monitor a live interface in real time

Run with privileges for raw socket access.

```
sudo python3 Source/main.py
```

Outcome: in the GUI, select the network interface and **Start Live**; the graph panel refreshes about every 4 seconds with current flows, and stopping the capture runs covert-channel detection on what was collected before offering a point-in-time **Visualize!** snapshot. Root is required for live capture; file analysis runs without it.

*No corpus evidence — derived from upstream documentation.*
