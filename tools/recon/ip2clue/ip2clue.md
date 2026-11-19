---
name: "ip2clue"
categories:
  - blackarch-recon
version: "0.0.95"
homepage: "http://kernel.embedromix.ro/us/"
packaged-by: [blackarch]
blackarch-builddate: "2019-05-10"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Seabreg/ip2clue"
github-stars: 0
github-pushed: "2019-05-19"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ip2clue

> High-performance IPv4/IPv6 country-lookup daemon fronting several geolocation databases with a simple TCP query protocol.

## What it's for

ip2clue (Catalin(ux) M. BOIE) is a daemon, `ip2clued`, that loads one or more IP-to-country databases — MaxMind GeoLite (IPv4 and IPv6), Software77, WebHosting.info and IP2Location formats are supported — and answers country lookups over a text protocol on TCP port 9999 (default), built on the Conn library for high concurrency (the author reports over 10,000 requests per second). A small client binary ships alongside the daemon. Databases are configured in `/etc/ip2clue/ip2clued.conf` with automatic downloads set up in `/etc/ip2clue/download.conf`. It is infrastructure for applications needing local IP geolocation, not an interactive tracker.

## Typical use cases

### Look up an IP's country through the daemon

Use the bundled client against a running ip2clued.

```
ip2clue 198.51.100.10
```

Outcome: the client connects to the daemon (default port 9999, overridable as a second argument), issues the `R<ip>` query and prints a line shaped like `OK ip=198.51.100.10 cs=<country code> cl=<country name> tz= isp=`.

### Inspect daemon statistics

Check database state and query counters.

```
telnet 127.0.0.1 9999
S
```

Outcome: connected to the daemon's text protocol, the `S` command prints statistics such as loaded database entries and query counts; raw lookups (`R<ip>`) can be issued the same way for debugging clients in other languages.

*No corpus evidence — derived from upstream documentation.*
