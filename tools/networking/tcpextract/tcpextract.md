---
name: "tcpextract"
categories:
  - blackarch-networking
version: "1.1"
homepage: "https://github.com/faust/tcpextract"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "faust/tcpextract"
github-stars: 49
github-pushed: "2013-02-14"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tcpextract

> Carves transferred files out of TCP sessions — reads pcap files or sniffs live traffic and reassembles HTTP responses into the original files.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Recovering files that crossed the wire from a packet capture, without manually reassembling TCP streams. It sits on libnids (via pynids) for stream reassembly and scans each session with pluggable extractor modules; in version 1.1 the shipped module recognizes HTTP GET responses, and the framework is designed so new protocol modules can be added. Live sniffing works but the author notes it is slow — the intended workflow is to capture with tcpdump first and feed the pcap file in. Live capture requires root. It is an extractor, not a sniffer suite: it does not write captures, decrypt anything, or parse protocols beyond its loaded modules.

## Typical use cases

### Extract files from a pcap capture

```
tcpextract -f capture.pcap -d /tmp/loot
```

Outcome: every TCP session in the capture is reassembled and scanned; recovered files are written into the output directory (`-d`, default `./output`). Success is simply finding the transferred files there — HTTP GET responses are dumped as the files the server sent.

### Sniff a live interface and extract on the fly

```
sudo tcpextract -i eth0 -d /tmp/loot
```

Outcome: captures on the given interface (`-i`, default `all`), prints `To stop: kill <pid>` so you know how to terminate it, and writes recovered files to the output directory as sessions complete. Root is required for live capture; for faster results prefer `tcpdump -w` plus the offline form above.
