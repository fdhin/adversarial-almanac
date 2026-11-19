---
name: "torshammer"
categories:
  - blackarch-dos
version: "1.0"
homepage: "http://sourceforge.net/projects/torshammer/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "dotfighter/torshammer"
github-stars: 341
github-pushed: "2022-04-23"
github-archived: false
github-issues: 8
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# torshammer

> Slow POST HTTP DoS tester (Python) with optional routing through a local Tor SOCKS proxy.

## What it's for

Tor's Hammer is a slow-POST denial-of-service tester in the [[slowloris]]
family: instead of dribbling partial headers, it opens HTTP connections and
sends POST bodies a few bytes at a time, tying up one thread per connection
on thread-per-connection servers (Apache 1.x, older IIS fall around ~128
threads; newer IIS and Apache 2.x around ~256, per the author's notes). Its
distinguishing feature is `-T`, which routes the flood through a Tor daemon
assumed to be listening on 127.0.0.1:9050. Defaults are port 80 and 256
threads. The packaged tool runs under Python 2. Use only against servers
you own or are authorized to test.

## Typical use cases

### Run a slow-POST flood directly

Exhaust an unprotected test server's connection handlers.

```
torshammer -t target.example.com -r 256
```

Outcome: spawns 256 threads that each open a POST to target.example.com:80
and trickle the body; on the target, worker/thread consumption climbs until
the service stops responding. Stop with Ctrl+C.

### Route the flood through Tor

Anonymize the source through a local Tor daemon on 127.0.0.1:9050.

```
torshammer -t target.example.com -p 80 -T -r 128
```

Outcome: same slow-POST flood, but every connection exits through the local
Tor SOCKS proxy (requires Tor running locally); the server's logs show Tor
exit-node addresses instead of your own.

### Review the interface

Print the option summary.

```
torshammer -h
```

Outcome: prints the usage line — `-t` target host/IP, `-r` threads (default
256), `-p` port (default 80), `-T` enable Tor — confirming the flag set
before scripting a test.

*No corpus evidence — derived from upstream documentation.*
