---
name: "replayproxy"
categories:
  - blackarch-forensic
version: "1.1"
homepage: "https://code.google.com/archive/p/replayproxy/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-14"
build-system: scripts
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "sparrowt/replayproxy"
github-stars: 23
github-pushed: "2022-01-19"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# replayproxy

> Re-live a captured HTTP session: replays the responses stored in a pcap file through a local HTTP proxy.

## What it's for

replayproxy (originally by Armin Buescher, hosted on Google Code; the linked GitHub repo is a bug-fixed fork of the same tool) lets you re-experience a web session that was captured in a pcap file. It parses the HTTP streams out of the capture, caches them, and starts a local HTTP proxy; when your browser — configured to use that proxy — requests a URL that matches a captured request, it is served the recorded response exactly as captured, while non-matching requests are ignored. Use it to re-trace a web-based attack or browsing session for forensic review or demonstration without touching the original server. It needs the pcap as input and parses only HTTP traffic (built on dpkt and pynids, Python 2 era).

## Typical use cases

### Serve a captured session on localhost

Start the proxy with defaults and point a browser at it.

```
replayproxy capture.pcap
```

Outcome: the HTTP streams in the pcap are parsed and cached, and a proxy starts listening on 127.0.0.1:3128; configure the browser to use that proxy and browse to the captured site to see the recorded responses replayed.

### Bind the proxy to a specific address and port

Run it where an analysis workstation can reach it.

```
replayproxy -H 198.51.100.10 -p 8080 capture.pcap
```

Outcome: the proxy listens on 198.51.100.10:8080 instead of the defaults, serving recorded responses from the capture to any browser configured with that proxy.

### Watch the matching live

Raise verbosity to see requests being matched against the capture.

```
replayproxy -vv capture.pcap
```

Outcome: DEBUG-level logging shows each incoming request and which captured response (if any) it is answered with — useful to confirm why a page replays or silently fails to match.

*No corpus evidence — derived from upstream documentation.*
