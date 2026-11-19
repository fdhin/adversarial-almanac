---
name: "bgp-md5crack"
categories:
  - blackarch-cracker
version: "0.1"
homepage: "http://www.c0decafe.de/"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-24"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# bgp-md5crack

> Recovers RFC 2385 TCP-MD5 passwords (BGP session keys) from a packet capture or a live interface.

## What it's for

BGP peers commonly authenticate their TCP sessions with the MD5 signature option defined in RFC 2385. bgp-md5crack takes a pcap containing such an authenticated session — or a live capture — and recovers the shared password, either from a wordlist or by brute force, by recomputing the MD5 signature for each candidate. Use it to audit the strength of peering keys whose traffic you can legitimately capture; without at least one signed packet there is nothing to attack. The BlackArch package ships a starter wordfile and sample captures under /usr/share/bgp-md5crack/.

## Typical use cases

### Crack a session key from a packet capture

```
bgp-md5crack -w /usr/share/bgp-md5crack/wordfile -f session.pcap
```

Outcome: parses the MD5-signed TCP packets from session.pcap and prints the password when a wordlist entry reproduces the signature.

### Brute-force the password instead

```
bgp-md5crack -b -a -f session.pcap
```

Outcome: iterates candidate passwords against the captured signature; `-a` restricts the search to the alphanumeric keyspace, without it the full character space is tried.

### Listen live and stop at the first hit

```
sudo bgp-md5crack -w wordfile -i eth0 -s -v
```

Outcome: captures on eth0 (root required for live capture) and tests each candidate as signed packets arrive; `-s` stops after the first password found and `-v` prints progress.

*No corpus evidence — derived from upstream documentation.*
