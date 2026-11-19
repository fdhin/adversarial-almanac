---
name: "hetty"
categories:
  - blackarch-webapp
version: "v0.7.0.r7.gf7def87"
homepage: "https://github.com/dstotijn/hetty"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-12-05"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "dstotijn/hetty"
github-stars: 12534
github-pushed: "2026-07-21"
github-archived: false
github-issues: 54
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# hetty

> HTTP toolkit for security research in a single binary: MITM proxy, request crafter/replayer and a web-based admin interface.

## What it's for

Hetty (by dstotijn) packs a Burp-Suite-style workbench into one Go binary: a machine-in-the-middle HTTP proxy with logging and search, an HTTP client for crafting and replaying requests, interception for manual review/editing of traffic in flight, scope control, and project-based database storage — all driven from a browser-based admin UI. Point a browser or HTTP-speaking tool at it and every request/response flows through an inspectable, editable history. It is under active development and aims at the infosec/bug-bounty workflow; for HTTPS interception its generated CA (~/.hetty/hetty_cert.pem) must be trusted by the client.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Start the proxy and admin interface

The default single-binary launch.

```
hetty
```

Outcome: listens on `:8080` (admin UI at `http://localhost:8080`, proxy on the same address); on first run a CA keypair is generated under `~/.hetty/` — import `hetty_cert.pem` into the client's trust store to intercept HTTPS.

### Bind a custom address and database

Keep projects isolated or bind off the default port.

```
hetty --addr 127.0.0.1:9090 --db engagement.db
```

Outcome: proxy and admin UI listen on 127.0.0.1:9090 and all projects/requests are stored in `engagement.db` instead of the default `~/.hetty/hetty.db` (`--cert`/`--key` override the TLS keypair paths the same way).

### Launch a preconfigured Chrome for interception

Skip manual browser proxy setup.

```
hetty --chrome
```

Outcome: hetty starts and spawns a Chrome instance already pointed at its proxy with certificate errors ignored — a ready-made interception sandbox for testing.
