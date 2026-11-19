---
name: "mitm-proxy-ja3-ja4"
categories:
  - scanner
version: "git (2026-11-02)"
homepage: "https://github.com/3radiance/mitm-proxy-ja3-ja4"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: rust
meta-checked: "2026-11-09"
github-repo: "3radiance/mitm-proxy-ja3-ja4"
github-stars: 19
github-pushed: "2026-11-02"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 42
gh-activity-checked: "2026-11-09"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# mitm-proxy-ja3-ja4

> Rust MITM proxy whose TLS, HTTP/2 and TCP fingerprints are fully spoofable from YAML profiles.

## What it's for

An intercepting HTTP(S) proxy (tokio + BoringSSL via btls) built for fingerprint control. The upstream connection's ClientHello — cipher suites, curves, signature algorithms, extensions and their exact order, GREASE, ECH, ALPS, session tickets — is constructed from a YAML profile, as are the HTTP/2 SETTINGS/priority/header ordering (the Akamai fingerprint) and even the TCP SYN (TTL, window, MSS, option order) rewritten through NFQUEUE with automatic iptables management. The browser side is intercepted with a locally generated CA. Reach for it when a site fingerprint-blocks your scripts or scanner: present a Chrome-shaped TLS+H2 stack instead of a Rust-shaped one. All settings live in the YAML file passed with `-c` — there are no `--port`-style CLI flags.

## Typical use cases

### Intercept traffic with a spoofed fingerprint

Run the proxy and point a browser at it.

```
mitm-proxy-ja3-ja4 -c profile.yaml
```

Outcome: starts listening on the port configured in `profile.yaml`; on first run it creates `ca.crt`/`ca.key` at the configured paths — import and trust `ca.crt` in the browser, and its HTTPS traffic is intercepted while every upstream connection presents your configured TLS/HTTP2 fingerprint.

### Route specific domains to other profiles

Give one domain a different fingerprint.

```
mitm-proxy-ja3-ja4 -c profile.yaml -d domain.yaml
```

Outcome: connections are matched by SNI against the patterns in `domain.yaml` (exact, `*.root`, `label.*`) and served with per-domain TLS/HTTP/TCP overlays — only the fields that differ from the base profile need to be listed.

### Debug what the fingerprint layer sends

See the actual handshake parameters.

```
RUST_LOG='TLS=debug,H2=error' mitm-proxy-ja3-ja4 -c profile.yaml
```

Outcome: prints per-module log lines (tags: TCP, HTTP, H1, H2, TLS, CA, ECH, CFG, IPT, NFQUEUE, Runtime) with timestamps, showing exactly what was offered upstream — correlate them with a packet capture to verify the spoof.
