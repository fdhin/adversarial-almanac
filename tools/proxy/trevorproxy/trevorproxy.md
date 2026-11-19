---
name: "trevorproxy"
categories:
  - blackarch-proxy
version: "1.0.6"
homepage: "https://github.com/blacklanternsecurity/TREVORproxy"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-14"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "blacklanternsecurity/TREVORproxy"
github-stars: 540
github-pushed: "2026-10-23"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# trevorproxy

> SOCKS proxy that gives every connection a different source address — round-robined through SSH tunnels or drawn from an entire routed subnet.

## What it's for

TREVORproxy (Black Lantern Security) is a local SOCKS5 server that randomizes the source IP address of each connection, written for WAF bypass, password spraying and web scraping. It has two modes: `ssh` opens an `ssh -D` SOCKS tunnel to each remote host you list and installs iptables rules that send each new connection through the next tunnel in turn; `subnet` (root, Linux only) uses the kernel's AnyIP feature to source every connection from a random address out of a subnet you genuinely route — for example a provider-assigned IPv6 /64. It is a fully functioning SOCKS proxy that expects return traffic, not a packet spoofer, so it is explicitly not a DoS tool. Global listen options apply in both modes; the `ssh` mode is limited by how many exit hosts you can reach, `subnet` mode by the address space actually routed to you.

## Typical use cases

### Rotate source IPs through SSH exit hosts

Round-robin every connection across a list of remote hosts.

```
trevorproxy ssh -k ~/.ssh/id_rsa user@198.51.100.10 user@198.51.100.20
```

Outcome: opens one `ssh -D` SOCKS tunnel per host (using the given key), creates the iptables statistic rules that distribute connections across them, and serves SOCKS5 on 127.0.0.1:1080 — repeated `curl --proxy socks5://127.0.0.1:1080 ifconfig.me` calls report a different exit host each time. Point proxychains or spraying tooling at the same listener.

### Send each connection from a random address of your subnet

Use a routed subnet as an address pool (Linux, root).

```
sudo trevorproxy subnet -s 2001:db8::/64 -i eth0
```

Outcome: adds a local route for the subnet on the interface (`ip route add local <subnet> dev <interface>`) and listens on socks5://127.0.0.1:1080; every connection leaves from a fresh random source address inside the subnet, visible as a different client IP per request at the destination. The subnet must genuinely be routed to you — `2001:db8::/64` here is the documentation placeholder for e.g. a provider-assigned /64.

### Expose the rotating proxy to other hosts

Bind a shared listener instead of loopback-only.

```
trevorproxy -l 0.0.0.0 -p 1081 ssh user@198.51.100.10 user@198.51.100.20
```

Outcome: the SOCKS5 listener binds 0.0.0.0:1081 (defaults are 127.0.0.1:1080) so tooling on other hosts can use the rotation; the global listen flags go before the mode subcommand.

*No corpus evidence — derived from upstream documentation.*
