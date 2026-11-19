---
name: "ptunnel"
categories:
  - post-exploitation
version: "0.72-7"
homepage: "https://www.cs.uit.no/~daniels/PingTunnel"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-04-09"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "f1vefour/ptunnel"
github-stars: 46
github-pushed: "2011-11-01"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ptunnel

> Tunnels TCP connections through ICMP echo request/reply packets when only ping can get out.

## What it's for

ptunnel (Ping Tunnel, by Daniel Stoedle) moves TCP traffic inside ICMP echo requests and replies, which lets it cross networks that block TCP and UDP but still answer ping. A single binary plays both roles: the proxy runs as root on a host the client can ping and relays the ICMP-embedded streams to real TCP destinations; the client runs on the restricted host and exposes the tunnelled service on a local port. The canonical job is reaching an SSH daemon when only ICMP egress exists, but any TCP service can be carried. Both roles need libpcap and usually root; a quick sanity check is that the client must be able to ping the proxy host at all. The same design lives on in the actively maintained successor ptunnel-ng for current systems.

## Typical use cases

### Start the proxy on a ping-reachable host

```
sudo ptunnel -c eth0
```

Outcome: the proxy captures ICMP echo traffic on eth0, answers ptunnel sessions addressed to it, and relays their embedded TCP streams to the requested destinations; `-c` names the capture device when autodetection picks the wrong one.

### Tunnel SSH over ICMP from a restricted client

```
sudo ptunnel -p 198.51.100.10 -lp 8000 -da target.example.com -dp 22
ssh -p 8000 127.0.0.1
```

Outcome: the client encapsulates the session in ICMP echo packets addressed to the proxy at 198.51.100.10 and forwards local port 8000 through it to target.example.com:22 — the `ssh` command lands on the target's sshd as if it were listening locally.

### Restrict the proxy to holders of a passphrase

```
sudo ptunnel -x long-random-passphrase
```

Outcome: clients may only use the proxy when they pass the same `-x` passphrase (sent hashed, never in the clear); note the passphrase is visible in `ps` output on both hosts, so treat it as access control rather than secrecy.

*No corpus evidence — derived from upstream documentation.*
