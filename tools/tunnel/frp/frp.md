---
name: "frp"
categories:
  - tunnel
version: "git (2026-11-05)"
homepage: "https://github.com/fatedier/frp"
origin: "kitploit"
recipe: "upstream"
packaged-by: [nixpkgs]
build-system: go
meta-checked: "2026-11-09"
github-repo: "fatedier/frp"
github-stars: 110492
github-pushed: "2026-11-05"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 198
gh-activity-checked: "2026-11-09"
worth: 9.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 36
---

# frp

> Fast reverse proxy in Go: a public-side server (`frps`) and a NAT-side client (`frpc`) that dials out and exposes local services on the public address.

## What it's for

Reaching services on machines that cannot accept inbound connections — behind NAT or a firewall that only lets traffic out. `frps` runs on a host with a public IP, `frpc` runs on the internal machine and keeps an outbound connection open to it; proxies declared in `frpc`'s TOML configuration map public ports or vhost domains onto local services. Proxy types include TCP, UDP, HTTP/HTTPS virtual hosting, Unix domain sockets, and secret (pre-shared) proxies; authentication can be token or OIDC, with optional TLS between client and server. Two static binaries, one per role. It is an exposure tool, not an anonymizer — it forwards traffic and adds auth between the frp components, but says nothing about what the exposed service itself allows.

## Typical use cases

### Expose an SSH service behind NAT

```
# frps.toml (public server 198.51.100.10)
bindPort = 7000

# frpc.toml (internal host)
serverAddr = "198.51.100.10"
serverPort = 7000

[[proxies]]
name = "ssh"
type = "tcp"
localPort = 22
remotePort = 6000

# start: ./frps -c ./frps.toml on the server, ./frpc -c ./frpc.toml internally
```

Outcome: `ssh -oPort=6000 user@198.51.100.10` reaches the internal host's SSH. `remotePort` is opened on the public server, `localPort` is the service behind frpc, and `serverPort` carries only the frpc-to-frps control channel — the three are independent.

### Expose a local web app on a vhost domain

```
# frps.toml
bindPort = 7000
vhostHTTPPort = 8080

# frpc.toml
serverAddr = "198.51.100.10"
serverPort = 7000

[[proxies]]
name = "web"
type = "http"
localPort = 80
customDomains = ["www.example.com"]
```

Outcome: with www.example.com's A record pointed at the frps host, `http://www.example.com:8080` serves the internal machine's port 80. Routing is by Host header, so many internal web apps share one public port; `vhostHTTPSPort` does the same for TLS.

### Relay UDP through the public server

```
# frpc.toml
serverAddr = "198.51.100.10"
serverPort = 7000

[[proxies]]
name = "dns"
type = "udp"
localIP = "8.8.8.8"
localPort = 53
remotePort = 6000
```

Outcome: `dig @198.51.100.10 -p 6000 example.com` resolves through the frpc host's network path. `localIP` can be any host reachable from frpc, so the proxy acts as a generic UDP relay, not just a forward to the frpc machine itself.

### Require a shared token between frpc and frps

```
# add to frps.toml and frpc.toml alike
auth.method = "token"
auth.token = "Password1"
```

Outcome: frpc connections that cannot present the same token are rejected during login. Without it, anyone who can reach `bindPort` can register proxies on the public address.

*No corpus evidence — derived from upstream documentation.*
