---
name: "pr0cks"
categories:
  - blackarch-proxy
version: "20.bcfcf2d"
homepage: "https://github.com/n1nj4sec/pr0cks"
packaged-by: [blackarch]
blackarch-builddate: "2022-10-30"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "n1nj4sec/pr0cks"
github-stars: 19
github-pushed: "2023-04-27"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pr0cks

> Transparent proxy that routes all of a host's TCP and DNS traffic through a SOCKS5 server via iptables REDIRECT — no per-application proxy support required.

## What it's for

pr0cks (by Nicolas Verdier, n1nj4sec) pairs a small DNS server with a transparent TCP redirector, both written in Go: iptables nat rules push outbound TCP (and UDP/53 DNS) to local listeners, and pr0cks forwards everything through a SOCKS5 upstream such as an SSH dynamic forward. This makes tools with no native proxy support — scanners, database clients, anything on the box — transparently egress through the pivot host. The earlier Python release also supported HTTP CONNECT upstreams; the current Go version takes SOCKS5 only. It needs root for the iptables rules and for binding its listeners (defaults: TCP on `127.0.0.1:10080`, DNS on `127.0.0.1:10053`).

## Typical use cases

### Pivot all host traffic through an SSH dynamic forward

```
ssh -D 1080 user@pivot.example.com
sudo pr0cks -socks5 127.0.0.1:1080
```

Outcome: pr0cks starts its local DNS (127.0.0.1:10053) and TCP (127.0.0.1:10080) listeners forwarding to the SOCKS5 server; once the redirect rules below are loaded, matching traffic egresses from the pivot host.

### Install the iptables redirection rules

```
sudo iptables -t nat -A OUTPUT -o lo -j ACCEPT
sudo iptables -t nat -A OUTPUT -d 198.51.100.0/24 -p tcp -j REDIRECT --to-ports 10080
sudo iptables -t nat -A OUTPUT -d 198.51.100.0/24 -p udp --dport 53 -j REDIRECT --to-ports 10053
```

Outcome: TCP toward the target range and its DNS queries are redirected into pr0cks (mirrors the repo's `example-iptables.sh`, which scopes the rules to a destination CIDR so loopback and local traffic stay untouched); remove the rules to stop redirection.

### Resolve names through the tunnel with a chosen upstream

```
sudo pr0cks -socks5 127.0.0.1:1080 -dns 198.51.100.10:53
```

Outcome: DNS queries hitting the local listener are forwarded through the SOCKS5 tunnel to the given resolver instead of the default `8.8.8.8:53`, so name resolution happens from the pivot's vantage point.

*No corpus evidence — derived from upstream documentation.*
