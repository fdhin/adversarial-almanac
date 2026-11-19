---
name: "sessionlist"
categories:
  - blackarch-networking
version: "6.3efc3b2"
homepage: "http://www.0xrage.com/"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-15"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "iamrage/sessionlist"
github-stars: 9
github-pushed: "2013-08-12"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sessionlist

> Sniffs cleartext HTTP for reusable session cookies and other authentication data.

## What it's for

sessionlist (by rage, 0xrage.com) is a small libpcap-based sniffer that watches outbound HTTP (TCP port 80 by default) and reassembles the interesting authentication material — session cookies and related headers — from sites that rely on non-secure cookie auth. Every captured session, with its metadata (peer IPs, payload size), is appended to sessions.txt in the working directory, ready to be replayed in a browser via a header-modifying extension. It has no TLS capability of its own: pair it with an existing SSL-stripping or MITM setup and run it against the interface carrying the decrypted traffic.

## Typical use cases

### Harvest session cookies off the wire

Collect replayable sessions from traffic crossing your test segment.

```
sudo sessionlist eth0
```

Outcome: sniffs eth0 for outbound HTTP, reconstructs the authentication data it carries, and appends each newly seen session with IP metadata to sessions.txt; load a captured Cookie/User-Agent pair into a browser header-modifying extension to confirm the session replays as the victim.

### Watch a non-standard HTTP port

Cover web traffic that does not ride on port 80.

```
sudo sessionlist eth0 8080
```

Outcome: same capture restricted to destination port 8080 — e.g. internal applications or the cleartext side of a stripping proxy.

*No corpus evidence — derived from upstream documentation.*
