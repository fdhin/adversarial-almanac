---
name: "tpcat"
categories:
  - blackarch-misc
version: "latest"
homepage: "http://sourceforge.net/projects/tpcat/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "jlongman/tpcat"
github-stars: 4
github-pushed: "2017-06-29"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tpcat

> wxPython GUI that diffs two packet captures for dropped or injected packets.

*No corpus evidence — derived from upstream documentation.*

## What it's for

TPCAT (Tim Eberhard, 2009) is a packet-capture comparison tool built on the
EFF's pcapdiff code: capture the same traffic at two points in the network
(for example either side of a firewall), load both pcap files, and it
reports packets present in the source capture but missing at the destination
(dropped or filtered) as well as packets that appear only on the far side
(injected). It answers "is something on this path modifying my traffic?" —
a firewall/IPS test, not a general analyzer. The packaged tool is a Python 2
wxPython GUI with no command-line flags; the linked GitHub repository
(`jlongman/tpcat`) hosts a fork of the same code that replaces the GUI with
an argparse CLI.

## Typical use cases

### Compare two captures taken around a middlebox

Detect filtering or injection on a network path.

```
tpcat
```

Outcome: opens the wxPython window; enter the flow's source and destination
IP addresses, load the two .pcap files captured at each end of the path,
and the analysis lists every dropped packet with its source, destination and
IP ID (plus any packets seen only on the far side) — repeated "Packet
Dropped" lines with matching IP IDs are the packets the middlebox removed.
