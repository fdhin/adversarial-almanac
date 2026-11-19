---
name: "udptunnel"
categories:
  - blackarch-networking
version: "19"
homepage: "https://code.google.com/archive/p/udptunnel/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-09-12"
build-system: make
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "githubsaturn/udptunnel"
github-stars: 4
github-pushed: "2015-09-09"
github-archived: false
github-issues: 6
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# udptunnel

> Daniel Meekins' TCP-over-UDP tunnel — a client that listens for local TCP and pipes it through a UDP carrier to a server that proxies to the real TCP destination, with per-rule ACLs on the server.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Carrying TCP sessions across a path where only UDP passes (or where you want TCP semantics hidden inside UDP datagrams). The single executable runs as server or client: the server listens on a UDP port and, on request, opens a TCP connection to the destination the client names; the client listens locally for TCP, relays the stream over UDP to the server, and back again. The server side can restrict who may connect and to what via a comma-separated ACL list (`s=` source IP, `d=` destination IP, `sp=`/`dp=` ports, `a=allow|deny`, default catch-all allow). IPv6 works on both ends with `-6`. It tunnels one TCP stream per client port and does not encrypt or compress.

GitHub: automatic export of the retired code.google.com/p/udptunnel project (githubsaturn/udptunnel) — file tree and README identical to the packaged r19 tarball; not the same-named tunnels by rfc1036 (Columbia UDP-over-TCP), astroza or jingyu.

## Typical use cases

### Start the server-side UDP listener

```
udptunnel -s 4444
```

Outcome: the server listens on UDP 4444 and waits for clients; each connecting client names the TCP destination it wants, and the server opens that TCP connection on the client's behalf.

### Tunnel an SSH session through UDP

```
udptunnel -c 127.0.0.1 3333 198.51.100.10 4444 127.0.0.1 22
```

Outcome: the client listens for TCP on local port 3333 and carries it over UDP to the server at 198.51.100.10:4444, which connects to 127.0.0.1:22 on its own machine. `ssh -p 3333 user@127.0.0.1` now lands on the server's sshd via the UDP carrier (destination addresses are relative to the server).

### Restrict the server with an ACL

```
udptunnel -s 4444 s=198.51.100.10,a=deny dp=22,a=deny a=deny
```

Outcome: the server rejects tunnels from source 198.51.100.10, then rejects any destination port 22, and — because the final bare `a=deny` overrides the default allow-everything catch-all — denies everything not explicitly allowed; tune the rule order to allow exactly the relays you intend.

### Verbose IPv6 run

```
udptunnel -v -v -6 -s 2001:db8::10 4444
```

Outcome: the server binds to the IPv6 address 2001:db8::10 on UDP 4444 and prints two levels of debugging about every datagram and connection attempt (`-v` up to three times) — useful while first bringing a tunnel up.
