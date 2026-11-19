---
name: "pcapsipdump"
categories:
  - blackarch-voip
version: "0.2"
homepage: "http://pcapsipdump.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2020-07-23"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "jpterry/pcapsipdump"
github-stars: 17
github-pushed: "2016-03-05"
github-archived: false
github-issues: 5
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pcapsipdump

> Records each SIP call to its own pcap file, RTP and RTCP included.

## What it's for

pcapsipdump is a C++ capture tool that records live SIP sessions to disk tcpdump-style, but with one pcap file per call (keyed on the dialog's Call-ID) instead of a single bulk file — so thousands of concurrent sessions become thousands of individually openable call captures, each automatically including that call's RTP/RTCP media. It can also split an existing offline capture the same way, and can restrict which calls are kept by phone number or T.38 fax indication. It is purely a recorder — it neither analyses nor injects traffic.

## Typical use cases

### Record all live calls on an interface

```
pcapsipdump -i eth0 -d /tmp/captures
```

Outcome: daemonises and writes `/tmp/captures/<Call-ID>.pcap`, one file per SIP dialog containing the signalling plus RTP/RTCP (`-R rtp+rtcp` is the default; use `-R none` for signalling only, `-R rtp` to skip RTCP); `-f` keeps it in the foreground and `-U` makes writes packet-buffered so partially written files stay consistent.

### Split an existing capture into per-call files

```
pcapsipdump -r bulk.pcap -d /tmp/splits
```

Outcome: reads the offline pcap and rewrites it as one pcap per detected SIP session under /tmp/splits — no interface or special privileges needed.

### Keep only calls involving one number

```
pcapsipdump -i eth0 -n 5550123 -d /tmp/captures
```

Outcome: only sessions to or from number 5550123 are recorded; `-t` instead restricts capture to calls whose SDP indicates T.38 fax.

*No corpus evidence — derived from upstream documentation.*
