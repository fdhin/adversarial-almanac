---
name: "pwnat"
categories:
  - blackarch-networking
version: "14.d3c2b05"
homepage: "http://samy.pl/pwnat/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2022-04-13"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "samyk/pwnat"
github-stars: 3939
github-pushed: "2025-12-26"
github-archived: false
github-issues: 21
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# pwnat

> Samy Kamkar's NAT traversal tool — punches a direct client-to-server channel through two separate NATs with no port forwarding, no DMZ and no third party, by impersonating an ICMP traceroute hop.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Connecting a client behind one NAT to a server behind another when neither side can open or forward ports. The trick: the server continuously sends ICMP echo requests to a fixed dummy address (3.3.3.3) that never answers; a client that wants in forges an ICMP Time Exceeded packet quoting one of those pings, which most NATs treat as a legitimate return packet and deliver to the server — revealing the client's public address. UDP hole-punching (the technique of the author's earlier chownat, built on Daniel Meekins' udptunnel) then keeps a direct UDP channel open through both NATs, and TCP flows are tunneled over it, letting the client reach arbitrary hosts and ports through the server like a proxy. No relay, UPnP, DNS trick or spoofed middleman is involved — but success depends on NAT implementation details, so it works behind many NATs and firewalls, not all (method published at the IEEE P2P'10 conference).

## Typical use cases

### Start a server that anyone can tunnel through

```
pwnat -s
```

Outcome: the server begins emitting the ICMP probes to 3.3.3.3 and listens on UDP port 2222 (the default; pass a different port to change it); any pwnat client that can reach this host's public address may now punch a tunnel in.

### Bring a remote service to a local port

```
pwnat -c 8000 server.example.com example.com 80
```

Outcome: after the ICMP handshake and UDP punch-through, local TCP port 8000 forwards through the server to example.com:80 — browsing `http://localhost:8000` returns pages from example.com, with traffic carried end-to-end over the direct UDP tunnel.

### Tunnel an SSH session through both NATs

```
pwnat -c 2222 server.example.com 198.51.100.10 22
```

Outcome: local port 2222 becomes the tunnel's SSH entry point — `ssh -p 2222 user@localhost` reaches the SSH daemon at 198.51.100.10 via the pwnat server; any TCP-based service (IRC, FTP, game server, ...) rides the same way. Add `-v` (up to twice) for handshake debug output and `-6` for IPv6.

### Restrict what the server will proxy

```
pwnat -s 2222 198.51.100.10:22
```

Outcome: the server still listens on port 2222 but only builds tunnels whose final destination is 198.51.100.10 port 22 (several `host:port` pairs may be whitelisted), refusing to relay clients to anything else.
