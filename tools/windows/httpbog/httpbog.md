---
name: "httpbog"
categories:
  - blackarch-windows
version: "1.0.0.0"
homepage: "http://sourceforge.net/projects/httpbog/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/HTTPBog"
github-stars: 0
github-pushed: "2019-03-23"
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

# httpbog

> Slow-read HTTP denial-of-service tool that exhausts server sockets by reading responses as slowly as possible.

## What it's for

HTTP Bog (John Leitch, 2011) is a Windows console application for slow HTTP denial-of-service testing. Where slowloris-style tools hold connections open with trickled request headers or POST bodies, Bog instead opens connections, requests a resource, and then consumes sockets by reading the response body as slowly as it can — pinning the server's connection/worker pool for the duration. Connection creation rate, maximum socket count, read cadence and timeouts are all tunable, and a live status display shows sockets held and bytes read. Use it only against systems you are authorized to load-test; it performs no reconnaissance and needs a reachable URL, nothing more.

## Typical use cases

### Run a basic slow-read attack

Default settings against one URL.

```
httpbog -url http://target.example.com/
```

Outcome: opens connections to the URL and reads responses at the default slow cadence while a status display cycles (target URL, connected sockets, bytes read); the target's socket pool drains as connections accumulate — stop with Ctrl-C.

### Tune socket pressure

Control how fast the connection pool is built.

```
httpbog -url http://target.example.com/ -ClientCount 4096 -CreateCount 64 -CreateSleep 500
```

Outcome: grows the attack toward 4096 held sockets, creating 64 connections every 500 ms — slower ramp-up looks more like organic traffic and keeps the client under NAT/firewall limits; defaults are 16384 max, 256 per 250 ms.

### Control read pacing and timeouts

Match the read speed to the target's keep-alive behavior.

```
httpbog -url http://target.example.com/file.exe -ReadSleepMin 1000 -ReadSleepMax 10000 -Timeout 8000
```

Outcome: each read from a client sleeps a random 1000–10000 ms, holding each response open as long as possible, with the TCP send/receive timeout set to 8 s; large or slow responses (file downloads, CMS pages) keep sockets busy longest. Add `-Silent` to drop the status display.

*No corpus evidence — derived from upstream documentation.*
