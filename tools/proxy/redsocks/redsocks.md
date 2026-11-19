---
name: "redsocks"
categories:
  - blackarch-proxy
version: "211.19b822e"
homepage: "https://github.com/darkk/redsocks"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2019-08-24"
build-system: make
license: Apache
meta-checked: "2026-11-09"
github-repo: "darkk/redsocks"
github-stars: 3627
github-pushed: "2024-06-29"
github-archived: false
github-issues: 77
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# redsocks

> Transparently route any TCP connection through a SOCKS4/SOCKS5 or HTTPS proxy using firewall redirection.

## What it's for

redsocks is a daemon that transparently redirects any TCP connection to a SOCKS4, SOCKS5 or HTTPS (HTTP CONNECT) proxy. You pair it with iptables REDIRECT (or nftables) rules, so the redirection can be system-wide or network-wide; the original development goal was routing selected traffic through an OpenSSH DynamicForward SOCKS5 port. It supports login/password authentication for SOCKS5 and HTTPS, UDP relay via SOCKS5, and two DNS helpers: `dnstc` (a fake DNS server that answers every UDP query with a "truncated" reply, pushing RFC-compliant resolvers onto DNS-over-TCP) and `dnsu2t` (a DNS UDP-to-TCP relay). It is not the right tool for redirecting traffic into Tor (use Tor's own TransPort), its `http-relay` module for plain HTTP proxies is a broken hack vulnerable to CVE-2009-0801, and it acts at the TCP level — the three-way handshake completes locally, so it is not a "really" transparent proxy.

## Typical use cases

### Validate the configuration file

Check a config before deploying it.

```
redsocks -t -c /etc/redsocks.conf
```

Outcome: redsocks parses the config and exits without starting the daemon; syntax errors are reported with the offending line, so a clean exit means the file is safe to run.

### Run the redirector daemon

Start redsocks against its config.

```
redsocks -c /etc/redsocks.conf -p /var/run/redsocks.pid
```

Outcome: the daemon starts and listens on the `local_ip`/`local_port` from the config, writing its PID to the named file; send SIGUSR1 to dump the list of connected clients to the log, SIGTERM to shut down and close all active connections.

### Socksify one user's outbound traffic

Redirect a chosen user's TCP connections into redsocks and onward through the configured proxy.

```
iptables -t nat -N REDSOCKS
iptables -t nat -A REDSOCKS -d 0.0.0.0/8 -j RETURN
iptables -t nat -A REDSOCKS -d 10.0.0.0/8 -j RETURN
iptables -t nat -A REDSOCKS -d 100.64.0.0/10 -j RETURN
iptables -t nat -A REDSOCKS -d 127.0.0.0/8 -j RETURN
iptables -t nat -A REDSOCKS -d 169.254.0.0/16 -j RETURN
iptables -t nat -A REDSOCKS -d 172.16.0.0/12 -j RETURN
iptables -t nat -A REDSOCKS -d 192.168.0.0/16 -j RETURN
iptables -t nat -A REDSOCKS -d 198.18.0.0/15 -j RETURN
iptables -t nat -A REDSOCKS -d 224.0.0.0/4 -j RETURN
iptables -t nat -A REDSOCKS -d 240.0.0.0/4 -j RETURN
iptables -t nat -A REDSOCKS -p tcp -j REDIRECT --to-ports 12345
iptables -t nat -A OUTPUT -p tcp -m owner --uid-owner user -j REDSOCKS
```

Outcome: every TCP connection opened by `user`, except those to reserved addresses, is redirected to port 12345 where redsocks relays it through the proxy defined in /etc/redsocks.conf — applications need no proxy awareness. Swap the OUTPUT rule for `-A PREROUTING --in-interface <lan-if> -p tcp -j REDSOCKS` (with `local_ip` set to the router's address) to socksify a whole network behind the box.

*No corpus evidence — derived from upstream documentation.*
