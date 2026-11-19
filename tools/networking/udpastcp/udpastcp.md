---
name: "udpastcp"
categories:
  - blackarch-networking
version: "29.683b5e3"
homepage: "https://github.com/Hello71/udpastcp"
packaged-by: [blackarch]
blackarch-builddate: "2019-05-12"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Hello71/udpastcp"
github-stars: 5
github-pushed: "2019-05-09"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# udpastcp

> Alex Xu (Hello71)'s UDP-as-TCP disguiser — relays UDP datagrams between a client and a server instance, prepending synthetic TCP headers so middleboxes classify the flow as TCP and let it pass.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Getting UDP through firewalls that only permit TCP. A pair of instances bridges the gap: the client instance accepts your application's UDP locally, wraps each datagram with a crafted TCP header and ships it (still as UDP) to the server instance, which strips the header and delivers the original datagrams to the real destination. The author is explicit about the limits: there is no window scaling, a fixed window, no retransmission, and it is not designed to defeat rigorous deep packet inspection — only simple policy filters that key on the protocol field. The project is abandoned and the upstream README itself recommends `udp2raw-tunnel` as the practical alternative.

## Typical use cases

### Start the server instance next to the UDP destination

```
udpastcp -m server -p 11940 -H 127.0.0.1 -P 1194
```

Outcome: the server instance listens on port 11940 for the TCP-disguised datagrams and relays the unwrapped UDP payload to the actual service at 127.0.0.1:1194 (e.g. an OpenVPN server on its standard port).

### Start the client instance and point the UDP app at it

```
udpastcp -m client -p 1194 -H 198.51.100.10 -P 11940
```

Outcome: the client instance listens on local port 1194 for the application's UDP (e.g. `openvpn --proto udp --remote 127.0.0.1 1194`), wraps each datagram in a synthetic TCP header and sends it to the server at 198.51.100.10:11940 — on the wire the flow carries TCP-looking headers while remaining UDP transport end to end.

### Send through a specific interface with raw packets

```
udpastcp -m client -p 1194 -H 198.51.100.10 -P 11940 -d eth0
```

Outcome: `-d` switches to a libpcap raw-send path on the named interface, for cases where the default socket send of header+payload does not survive the local network stack; behavior is otherwise identical to the plain client run.
