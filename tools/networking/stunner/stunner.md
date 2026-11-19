---
name: "stunner"
categories:
  - blackarch-networking
version: "v0.5.8.r188.g7d918f4"
homepage: "https://github.com/firefart/stunner"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-11-01"
build-system: go
license: CC-BY-NC-SA-4.0
meta-checked: "2026-11-09"
github-repo: "firefart/stunner"
github-stars: 868
github-pushed: "2026-10-28"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 85
gh-activity-checked: "2026-11-09"
---

# stunner

> STUN/TURN server tester that turns a misconfigured TURN relay into a SOCKS proxy into the internal network.

## What it's for

stunner (by firefart, written in Go) probes STUN and TURN servers — including TURN over TCP per RFC 6062 and the IPv6 extension per RFC 6156 — for the misconfigurations that let a relay reach addresses it should not. It fingerprints the server, tests which private ranges it will forward to, and its headline feature runs a local SOCKS5 server that tunnels TCP through the TURN server, giving internal-network access from outside. Credentials for authenticated TURN servers must come from elsewhere (for example captured from a client); it also ships scanning, password-brute and memory-leak probes. Test tool — use against infrastructure you are authorized to assess.

## Typical use cases

### Fingerprint a STUN/TURN server

```
stunner info -s 198.51.100.10:3478
```

Outcome: prints what the server runs (software, version) and which protocols and attributes it supports — the first check of any TURN assessment.

### Discover which internal ranges the relay will reach

```
stunner range-scan -s 198.51.100.10:3478 -u user -p 'Password1' --protocol tcp
```

Outcome: reports which private and restricted ranges the TURN server will forward traffic to; any permitted range can then be enumerated with the `tcp-scanner` and `udp-scanner` commands.

### Pivot into the internal network over TURN

```
stunner socks -s 198.51.100.10:3478 -u user -p 'Password1' -x
```

Outcome: starts an unauthenticated SOCKS5 proxy on 127.0.0.1:1080 that relays TCP through the TURN server; point a browser or proxychains at it to reach internal addresses (`-x` drops requests to public IPs).

### Enumerate supported transports

```
stunner brute-transports -s 198.51.100.10:3478 -u user -p 'Password1'
```

Outcome: lists the transport protocols the server will relay to internal systems — mostly the defaults, occasionally a custom protocol implementation worth a closer look.

*No corpus evidence — derived from upstream documentation.*
