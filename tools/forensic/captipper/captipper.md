---
name: "captipper"
categories:
  - blackarch-forensic
version: "74.3fb2836"
homepage: "http://www.omriher.com/2015/01/captipper-malicious-http-traffic.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "omriher/CapTipper"
github-stars: 722
github-pushed: "2023-03-16"
github-archived: false
github-issues: 15
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# captipper

> Replays malicious HTTP traffic from a PCAP through a live local web server, with an interactive console for exploring every object and conversation.

## What it's for

CapTipper (by Omri Herscovici) analyzes, explores and revives malicious HTTP traffic from an existing packet capture. Given a PCAP of, for example, an exploit-kit drive-by, it stands up a local web server that answers each URL exactly as the original malicious server did, so the researcher can re-visit the infection chain in a browser or point analysis tooling at it, and simultaneously opens an interactive console for inspecting hosts, objects and conversations (bodies, headers, embedded iframes, zip contents, hashes). It works only on HTTP inside an existing PCAP — it is not a live-traffic proxy and does not decode other protocols. The original tool is a 2015-era Python 2/3 script; BlackArch exposes it as `captipper`.

## Typical use cases

### Replay a malicious capture in a live web server

```
captipper malware-traffic.pcap -p 8080
```

Outcome: CapTipper parses the PCAP, prints the list of HTTP conversations found (request URI, content type, object name, size) and starts a web server on port 8080 that serves the exact responses the original servers sent. Browsing `http://127.0.0.1:8080/<host>/<URI>` (from an isolated analysis VM) re-delivers the original payload, and an interactive console prompt opens in the terminal for deeper inspection.

### Triage objects from the interactive console

```
hosts
body 2 1000
dump 6
```

Outcome: inside the CapTipper console — `hosts` prints the host/URI tree of the whole capture for a bird's-eye view; `body 2 1000` prints the first 1000 bytes of object 2's response body (the default is 256); `dump 6` writes object 6 from memory to disk for static analysis. Other built-ins include `info`, `hexdump`, `ungzip`, `ziplist`, `iframes`, `hashes`, and `vt` (looks a stored object's hash up on VirusTotal when an API key is configured).

*No corpus evidence — derived from upstream documentation.*
