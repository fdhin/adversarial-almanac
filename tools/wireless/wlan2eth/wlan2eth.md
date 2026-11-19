---
name: "wlan2eth"
categories:
  - blackarch-wireless
version: "1.3"
homepage: "https://www.willhackforsushi.com/resources/wlan2eth/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-14"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Seabreg/wlan2eth"
github-match: "search"
github-stars: 0
github-pushed: "2019-09-07"
github-archived: false
github-issues: 0
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wlan2eth

> Converts 802.11 monitor-mode packet captures into standard Ethernet-frame pcaps (Joshua Wright).

## What it's for

Many analyzers, IDS sensors and scripts only read capture files with an Ethernet link-layer type. wlan2eth rewrites a packet capture containing raw 802.11 frames — plain 802.11, Prism, radiotap or PPI link types — into a new pcap in which each data frame is re-encoded as a standard Ethernet frame, so ordinary Ethernet-oriented tooling can process wireless captures. It is a file converter, not a network bridge: it operates entirely offline on pcap files and takes exactly an input and an output file. The willhackforsushi release has no source repository of its own; it is mirrored on GitHub at Seabreg/wlan2eth, whose sources are byte-identical to the upstream 1.3 tarball.

## Typical use cases

### Rewrite a wireless capture as an Ethernet capture

```
wlan2eth wlan-capture.pcap ethernet-capture.pcap
```

Outcome: reads `wlan2eth infile outfile`-style arguments, converts every packet it can to Ethernet framing and writes `ethernet-capture.pcap` with an Ethernet link type (DLT_EN10MB); it finishes by reporting how many packets were converted. The output pcap now opens cleanly in tools that refuse radiotap/802.11 link types.

*No corpus evidence — derived from upstream documentation.*
