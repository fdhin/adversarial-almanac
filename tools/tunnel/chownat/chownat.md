---
name: "chownat"
categories:
  - blackarch-tunnel
version: "0.08b"
homepage: "http://samy.pl/chownat/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "aur-archive/chownat"
github-stars: 0
github-pushed: "2015-08-15"
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

# chownat

> Lets two peers behind two separate NATs communicate directly — no port forwarding, no DMZ, no third party — by punching a UDP hole between them (Samy Kamkar).

## What it's for

Connecting two machines that each sit behind their own NATing router, when neither side controls the router. Both peers run chownat as an unprivileged user; it establishes a direct UDP channel between them and opens a tunnel over it, so one peer can reach a service (a web server, SSH, anything TCP) running on the other NAT'd machine. The server side must know the client's address in advance; the successor `pwnat` lifts that requirement. The channel itself is not encrypted — treat it as connectivity, not confidentiality. It is a single Perl script, so it runs anywhere Perl does, including as a quick copy-and-run payload.

## Typical use cases

### Offer a local service through your NAT (server side)

On the peer that runs the service (say a web server on local port 8080):

```
chownat -s 8080 client.example.com
```

Outcome: chownat performs the punch toward the named client peer and, once the channel is up, anyone arriving through it is connected to the service already listening on your `localhost:8080`. No privileged user, router configuration, or forwarded port is needed on this side.

### Reach that service from behind your own NAT (client side)

On the other peer, which wants to use the service:

```
chownat -c 8080 server.example.com
```

Outcome: chownat opens a local tunnel on `localhost:8080`; any connection made there is carried over the punched UDP channel and comes out on the server peer's local service — `curl http://127.0.0.1:8080` now talks to the web server behind the other NAT. The last argument is the server peer's address.

### Agree on a communication port and debug the punch

Both peers, using a non-default UDP port:

```
chownat -d -c 8080 server.example.com 5555
```

Outcome: the two chownat processes talk over UDP port 5555 instead of the default 2222 (both ends must use the same port), and `-d` prints debug output about what the punch is doing — repeat `-d` for verbose mode. Use this when 2222 is filtered or you want visibility into the handshake.

*No corpus evidence — derived from upstream documentation.*
