---
name: "caido-cli"
categories:
  - blackarch-webapp
version: "0.57.1"
homepage: "https://caido.io/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-07-12"
build-system: other
license: custom:private
aliases: ["caido"]
meta-checked: "2026-11-09"
github-repo: "caido/caido"
github-stars: 2723
github-pushed: "2026-10-30"
github-archived: false
github-issues: 742
github-match: "search"
gh-checked: "2026-11-09"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 14
gh-activity-checked: "2026-11-09"
---

# caido-cli

> Headless build of the Caido intercepting proxy — replay, inject, scan and fuzz HTTP.

## What it's for

Caido is a web-application auditing proxy (an alternative to Burp Suite) built on a client/server architecture: the CLI binary is the server component that handles proxying, matching, replay, automated checks and HTTP history, while the GUI is served as a web application from that same instance. The `caido-cli` package ships the standalone server binary, which is the right choice for VPS/container deployments, multi-user setups, and headless boxes — you point a browser at the instance instead of running a desktop app. Certain advanced instance options (listen addresses, credential/CA management) are only exposed on the CLI. The desktop variant is covered separately as [[caido-desktop]]; both packages the same upstream.

## Typical use cases

### Start a local Caido instance

Launch the proxy and use it from your browser.

```
caido-cli
```

Outcome: starts the Caido instance, opens the web GUI in a browser tab, and creates/fetches the CA certificate used for TLS interception; configure your browser or system proxy to the instance's proxy listener and traffic appears in the HTTP history for match/replace, replay, scans and fuzzing.

### Run headless on a server

Host the instance without opening a local browser.

```
caido-cli -l 0.0.0.0:8080 --no-open
```

Outcome: the instance listens on all interfaces with the UI available at `http://<server>:8080` — do not expose it unauthenticated; `--ui-listen` and `--proxy-listen` split the UI and proxy listeners onto separate addresses when needed, and `--data-path` relocates the instance data directory (useful for containers).

### Import an internal CA for TLS interception

Make the proxy trusted inside a corporate PKI.

```
caido-cli --import-ca-cert corp-root.pem
```

Outcome: imports the given CA certificate (optionally `--import-ca-cert-pass` for a protected file) so the instance can intercept TLS to services whose certificates chain to your organisation's root; `--reset-credentials` and `--reset-cache` recover a locked-out or stale instance.

No corpus evidence — derived from upstream documentation (docs.caido.io CLI reference).
