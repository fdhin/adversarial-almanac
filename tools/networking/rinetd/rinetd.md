---
name: "rinetd"
categories:
  - blackarch-networking
version: "0.62"
homepage: "http://www.boutell.com/rinetd"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-02-01"
build-system: other
license: GPL
meta-checked: "2026-11-09"
github-repo: "samhocevar/rinetd"
github-stars: 1107
github-pushed: "2024-03-29"
github-archived: false
github-issues: 30
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rinetd

> Single-process TCP/UDP port redirector driven by a flat rules file.

## What it's for

rinetd ("internet redirection server", originally by Thomas Boutell, now maintained by Sam Hocevar) forwards TCP or UDP connections from one IP/port pair to another according to rules in /etc/rinetd.conf. Because it is one non-blocking process that handles any number of forwards, it is a lightweight way to expose services behind NAT or a firewall, to move a service to a new host without touching clients, or to shim a port on a machine that cannot run the real service. It is not a load balancer: each rule maps exactly one bind endpoint to one connect endpoint.

## Typical use cases

### Redirect a TCP port to another host

Forward a port on this machine to a service living elsewhere.

```
# /etc/rinetd.conf
0.0.0.0 8080 198.51.100.10 80
```

```
rinetd -f -c /etc/rinetd.conf
```

Outcome: rinetd runs in the foreground (drop `-f` to daemonize) and proxies every connection to port 8080 on any local address through to 198.51.100.10:80; `0.0.0.0` binds all local addresses, a specific address binds only that interface, hostnames and `/etc/services` names are accepted for both endpoints.

### Forward UDP with a lifetime cap

Relay connectionless traffic without leaking mappings forever.

```
# /etc/rinetd.conf
0.0.0.0 8000/udp 198.51.100.10 53 [timeout=3600]
```

Outcome: UDP datagrams arriving on local port 8000 are forwarded to 198.51.100.10:53, and an idle mapping is torn down after the per-rule timeout (default 72 seconds) instead of accumulating until resources run out.

### Sanity-check the installed build

Confirm the binary and configuration parse before relying on it.

```
rinetd -v
```

Outcome: prints the version number and exits (`-h` prints a short help message) — useful after an upgrade to verify the daemon that will claim the rules file.

*No corpus evidence — derived from upstream documentation.*
