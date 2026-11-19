---
name: "binproxy"
categories:
  - blackarch-proxy
version: "8.d02fce9"
homepage: "https://github.com/nccgroup/BinProxy/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-23"
build-system: scripts
license: AGPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "nccgroup/BinProxy"
github-stars: 176
github-pushed: "2022-12-01"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# binproxy

> An interactive, dissecting proxy for arbitrary TCP protocols — Burp-style inspection for non-HTTP traffic.

## What it's for

BinProxy (NCC Group, Ruby) proxies an arbitrary TCP connection and shows every message exchanged between client and server in a web UI (http://localhost:4567), optionally parsed into named fields via BinData record definitions you write. It exists for the protocols Burp cannot help with — custom binary C2 protocols, game and industrial protocols — letting you read, edit and forward individual messages mid-conversation. Without a dissector class it falls back to a plain hexdump per message. It can also act as a SOCKS or HTTP CONNECT proxy with destinations determined by the client, and can terminate TLS (`--tls`) with a self-signed or supplied certificate. It parses and edits traffic; it is not a fuzzer or an exploit framework.

## Typical use cases

### Forward a static host with a hexdump view

```
binproxy localhost 9000 example.com 9000
```

Outcome: a listener on localhost:9000 relays to example.com:9000, and the UI at http://localhost:4567 shows each message as a hexdump (default RawMessage, one record per packet) — the two-minute way to see what an unknown protocol actually sends. Point the client at the local port.

### Act as a SOCKS proxy that unwraps TLS

```
binproxy -S --tls 1080
```

Outcome: a SOCKS proxy on localhost:1080 whose remote destination is chosen by the connecting client; each tunneled TLS session is terminated with a self-signed certificate, so the UI shows the plaintext messages in both directions.

### Run a Burp substitute for HTTPS

```
binproxy -H --tls --tls-cert certs/example.com-cert.pem --tls-key certs/example.com-key.pem --class-name DumbHttp::Message localhost 8080
```

Outcome: an HTTP CONNECT proxy on localhost:8080 that MITMs tunneled TLS using the supplied certificate and key, parsing each request with the bundled DumbHttp dissector. Only CONNECT-tunneled traffic traverses it — plain HTTP does not, so it suits HTTPS clients whose trust store you control. Generate matching certs in a source checkout with `rake makecert[example.com]`.

### Drive endpoints from the web UI at runtime

```
binproxy
```

Outcome: the UI comes up with no listener defined; enter local and remote hostnames/IPs and ports there and click update — endpoints can be (re)defined without restarting, then point a client at the local port and watch the packets flow. Add `-c <class>` (or `--class-name`) to render messages with a custom BinData record you have defined.

*No corpus evidence — derived from upstream documentation.*
