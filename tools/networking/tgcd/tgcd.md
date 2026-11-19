---
name: "tgcd"
categories:
  - blackarch-networking
version: "1.1.1"
homepage: "https://sourceforge.net/projects/tgcd/files/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-10-18"
build-system: autotools
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "kirgene/tgcd"
github-stars: 21
github-pushed: "2019-01-03"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tgcd

> TCP/IP Gender Changer Daemon by Faraz V — pairs an outbound-only node inside a restricted network with a listening node outside it to expose internal services through firewalls; doubles as a plain port forwarder.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Reaching a service inside a firewalled LAN when no inbound connections are possible. A ConnectConnect (CC) node runs where it can reach both the internal service and the internet; it establishes an outbound control connection to a ListenListen (LL) node on the outside. When a client connects to the LL node, the CC node is signaled over that control channel and opens the connection to the internal service — so the path is always initiated from inside. A third mode, Port Forwarder (PF), is a standalone one-port forwarder with no pairing. Access control is available via tcp-wrappers (hosts_access) and an executable filter script that receives the incoming IP and accepts or rejects by exit code. It forwards TCP only and does not encrypt traffic by default (a simple key-based obfuscation exists).

## Typical use cases

### Start the outside ListenListen node

```
tgcd -L -p 8000 -q 1234
```

Outcome: daemonizes and listens on port 1234 for the CC node's control connection and on port 8000 for actual clients. This instance runs on the externally reachable host.

### Pair it with an inside ConnectConnect node

```
tgcd -C -s 127.0.0.1:80 -c 198.51.100.10:1234
```

Outcome: connects out to the LL node at 198.51.100.10:1234 (retrying at the `-i` interval) and registers itself as the relay for the internal service at 127.0.0.1:80. Once both nodes are up, the internal web server is reachable on port 8000 of the LL host.

### Plain port forwarding without a pair

```
tgcd -F -p 5000 -s target.example.com:22
```

Outcome: local port 5000 forwards to target.example.com:22 — a standalone relay, no LL/CC pairing involved.

### Run in the foreground with link obfuscation

```
tgcd -L -p 8000 -q 1234 -k 42 -n
```

Outcome: stays attached to the terminal (`-n`, no daemon) and XOR-obfuscates traffic to the peer with key 42 (`-k`, 0-255; both nodes must agree) — handy for debugging since log output goes to the console with `-l`/`-g`.
