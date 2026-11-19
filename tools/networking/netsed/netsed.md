---
name: "netsed"
categories:
  - blackarch-networking
version: "1.3"
homepage: "http://silicone.homelinux.org/projects/netsed/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-01-26"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "xlab/netsed"
github-stars: 63
github-pushed: "2017-06-18"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netsed

> sed for network traffic — Julien VdG's in-flight payload rewriter (continuing Michal Zalewski's original 2002 netsed) that applies sed-style substitution rules to the TCP/UDP packets it forwards.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Altering the contents of network conversations in real time as they pass through a host. netsed listens on a local port, forwards each connection to a remote host:port (TCP or UDP), and rewrites matching bytes of every forwarded packet according to sed-like rules `s/pat1/pat2[/expire]`, with hex escapes for non-printable bytes (`%0a%0d`), wildcards (`%*`), and per-connection substitution expiry. The author's stated applications: black-box protocol auditing (force changes into an ongoing proprietary exchange and watch how the peers react), fuzz-style integrity testing of applications, and content filtering. It can also run as a transparent proxy fed by an iptables REDIRECT rule on a gateway, so clients need no reconfiguration. Payload rewriting only, TCP and UDP; the original 0.01c was written by Michal Zalewski (lcamtuf) and is maintained by Julien VdG.

## Typical use cases

### Forward a TCP stream while rewriting strings

```
netsed tcp 8080 198.51.100.10 80 's/andrew/mike'
```

Outcome: listens on local port 8080 and forwards each connection to 198.51.100.10:80 with every occurrence of `andrew` in the payload replaced by `mike`; point the client software at port 8080 on this host instead of at the real server.

### Apply a rule only once per connection

```
netsed tcp 8080 198.51.100.10 80 's/andrew/mike/1'
```

Outcome: same forwarder, but the substitution fires only on the first match of each connection — handy for mutating a login or handshake exchange while leaving the remainder of the session untouched; an expiry character scopes it further (`i` incoming-only, `o` outgoing-only, e.g. `/i1`).

### Rewrite binary bytes and pad to keep the length

```
netsed tcp 8080 198.51.100.10 80 's/andrew/mike%00%00'
```

Outcome: replaces `andrew` with `mike\x00\x00` — HTTP-style hex escapes inject arbitrary eight-bit bytes including NULL and `/` (`%%` is a literal percent), and the NULL padding keeps packet length constant so length-checked parsers keep working; wildcards match variable content, e.g. `'s/start%*end/new'` collapses everything between `start` and `end`.

### Run as a transparent proxy behind iptables

```
netsed tcp 10101 0 0 's/andrew/mike'
iptables -t nat -A PREROUTING -s 198.51.100.10 -d 198.51.100.20 -p tcp --dport 12345 -j REDIRECT --to-ports 10101
```

Outcome: with rhost and rport set to 0, netsed takes each connection's real destination from the REDIRECTed packet instead of a fixed target, so traffic from 198.51.100.10 to 198.51.100.20:12345 is silently rewritten mid-path on the gateway without any client-side changes.
