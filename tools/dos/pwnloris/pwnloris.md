---
name: "pwnloris"
categories:
  - blackarch-dos
version: "14.7ad1486"
homepage: "https://github.com/h0ussni/pwnloris"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "h0ussni/pwnloris"
github-stars: 29
github-pushed: "2025-09-07"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pwnloris

> Python reimplementation of the slowloris technique that grinds a web server from one machine, with optional Tor routing.

## What it's for

pwnloris is a standalone Python 3 implementation of the slowloris idea (abusing CVE-2007-6750 / CVE-2012-5568): every thread opens HTTP connections, sends incomplete requests, then dribbles keep-alives so the server's connection slots fill and legitimate visitors are locked out — all from a single host. What sets it apart from the original [[slowloris]] and the Python port [[slowloris-py]] is that it keeps attacking until the server shows exhaustion, accepts `host[:port]` targets inline, and can route every socket through Tor SOCKS. Needs Python plus `pysocks`; only use it against systems you are authorized to test.

## Typical use cases

### Run with defaults against a host

Start the exhaustion attack on port 80 with stock settings.

```
./pwnloris.py example.com
```

Outcome: 8 threads open partial HTTP requests to example.com:80 and keep them alive (90 s keep-alive window, checked every 5 s); the tool continues until the server's connection pool shows exhaustion. Append a port inline as `host:port` (e.g. `example.com:8080`).

### Size and pace the attack manually

Scale threads and connection lifetime for a specific target.

```
./pwnloris.py 198.51.100.10:8080 -n 16 -k 120 -i 10
```

Outcome: 16 threads hold connections for up to 120 seconds with keep-alive checks every 10 seconds — the documented fully-specified form (`-n` threads, `-k` keep-alive seconds, `-i` check interval).

### Attack through Tor

Hide the source behind a local Tor SOCKS listener.

```
./pwnloris.py example.com -t -sh 127.0.0.1 -sp 9050
```

Outcome: every socket connects through the SOCKS proxy at 127.0.0.1:9050 (`-t` enables Tor mode; `-sh`/`-sp` override the defaults), so the server sees Tor exit traffic instead of your address.

*No corpus evidence — derived from upstream documentation.*
