---
name: "flowinspect"
categories:
  - blackarch-networking
version: "97.34759ed"
homepage: "https://github.com/7h3rAm/flowinspect"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "7h3rAm/flowinspect"
github-stars: 82
github-pushed: "2017-10-13"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# flowinspect

> Flow-reassembled content inspection: regex, fuzzy matching, libemu shellcode detection and YARA over live traffic or pcap.

## What it's for

flowinspect (by 7h3rAm) inspects network traffic at the flow level: it
uses libnids (via pynids) to defragment IP and reassemble TCP streams —
UDP is inspected per packet — and then runs the reassembled content
through one or more detection engines: RE2-backed regex matching, fuzzy
string matching, libemu shellcode detection, and YARA rules. Input is
either a live listening device or a pcap file; scope is narrowed with BPF
filters, direction selection (client-to-server, server-to-client, or
both), and Snort-style offset/depth modifiers. Matching flows can be
dumped to pcap for further analysis. It is a Python 2-era tool; the
packaged command is `flowinspect`.

## Typical use cases

### Watch live HTTP requests on the wire

Match traffic as it happens and read it as text.

```
flowinspect -d eth0 -c "^(GET|POST|HEAD|PUT).*" -f "tcp and port 80" -o print
```

Outcome: prints each matching HTTP request with its headers as streams
arrive on eth0, plus per-direction counters; `-o meta` instead shows match
offsets, sizes and the packets a match spans.

### Hunt a pattern in a capture

Locate a known indicator inside a reassembled stream.

```
flowinspect -p capture.pcap -s 'CollectGarbage\(.*mstime_malloc' -b32
```

Outcome: prints `[MATCH]` lines identifying the flow and the direction,
offset and byte range of the match, followed by a hexdump capped at 32
displayed bytes (`-b`).

### Detect shellcode in a capture

Run libemu heuristics over reassembled streams.

```
flowinspect -p shellcode.pcap -M
```

Outcome: flags flows whose content contains x86 shellcode per libemu's
GetPC heuristics; add `-y` to generate an emulator profile showing what
the detected shellcode does.

### Apply YARA rules to network streams

Reuse host forensics signatures against traffic.

```
flowinspect -p capture.pcap -R packed.yara
```

Outcome: matches the rule file against flow content in both directions
(`-R`); `-P`/`-Q` restrict rules to client-to-server/server-to-client
data only, and `-Z` additionally writes every matching flow to its own
pcap file.

*No corpus evidence — derived from upstream documentation.*
