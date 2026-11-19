---
name: "turner"
categories:
  - blackarch-networking
version: "32.fad9423"
homepage: "https://github.com/staaldraad/turner"
packaged-by: [blackarch]
blackarch-builddate: "2024-01-07"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "staaldraad/turner"
github-stars: 198
github-pushed: "2023-12-30"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# turner

> staaldraad's proof-of-concept for tunneling through permissive or open TURN (WebRTC relay) servers — sets up allocations and exposes local HTTP and SOCKS5 proxies whose traffic exits from the TURN server's network position.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Demonstrating the impact of a misconfigured TURN server: when a TURN server accepts arbitrary credentials (or traffic to any peer, not just media candidates), it can be abused as a generic relay. turner connects to such a server, performs the allocations and TCP channel setup itself, and runs local proxies whose egress is the TURN server — reaching hosts that server can reach but you cannot, the classic case being internal-only addresses such as the cloud metadata endpoint 169.254.169.254 from the server's vantage. Use it against TURN servers you are authorized to test; the background is documented in the rtcsec write-up on the Slack WebRTC TURN compromise. It does not crack TURN credentials — the server must be permissive/open or you must hold valid ones.

## Typical use cases

### Tunnel HTTP egress through an open TURN server

```
turner -server turn.example.com:3478 -http
```

Outcome: allocates on the TURN server and opens an HTTP proxy on 127.0.0.1:8080. Verify the exit point with `curl -x http://localhost:8080 http://ifconfig.me/ip` — the returned IP is the TURN server's, not yours.

### Authenticate and proxy in one step

```
turner -server turn.example.com:3478 -u user -p Password1 -http
```

Outcome: same local HTTP proxy on 127.0.0.1:8080, but authenticating to the TURN server with long-term credentials (`-u`/`-p`) during allocation — the shape of a legitimately-credenzialed but over-permissive relay.

### Run a SOCKS5 proxy (or both proxies) over TURN

```
turner -server turn.example.com:3478 -u user -p Password1 -socks5 -http
```

Outcome: a SOCKS5 proxy starts on 127.0.0.1:8000 (`-socks5`), alongside the HTTP proxy when `-http` is also given — SOCKS5 covers arbitrary TCP tools, not just HTTP clients, with all traffic exiting via the TURN allocation.
