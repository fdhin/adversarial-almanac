---
name: "sockstat"
categories:
  - blackarch-networking
version: "0.4.1"
homepage: "https://packages.debian.org/unstable/main/sockstat"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2019-12-24"
build-system: make
meta-checked: "2026-11-09"
github-repo: "mezantrop/sockstat"
github-stars: 30
github-pushed: "2026-08-16"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# sockstat

> FreeBSD-style table of open Internet, UDP and UNIX-domain sockets, by Mikhail Zakharov.

## What it's for

sockstat faithfully reproduces FreeBSD's classic same-named utility on other operating systems: a compact table of open sockets showing the owning user, process name and PID, protocol, and local and foreign addresses. On a busy host it answers "what is listening or connected, as which user, from which process" faster and more compactly than netstat or lsof. It is read-only introspection of the local host — it neither captures traffic nor touches remote systems. Run it as root to see other users' sockets, not just your own.

## Typical use cases

### List listening TCP sockets

```
sockstat -4 -T -l
```

Outcome: one line per listening IPv4 TCP socket with user, process/PID and local address:port — a quick audit of which services are exposed on the host.

### Show IPv6 and UNIX-domain sockets

```
sockstat -6 -U
```

Outcome: prints AF_INET6 and AF_LOCAL (UNIX-domain) sockets, revealing local IPC endpoints and IPv6 listeners that an IPv4-only check misses.

### Audit every socket on the host

```
sudo sockstat
```

Outcome: lists all sockets system-wide across every user's processes — the form to use when hunting an unexpected outbound connection or a hidden listener.

*No corpus evidence — derived from upstream documentation.*
