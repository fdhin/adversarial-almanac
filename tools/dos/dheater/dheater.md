---
name: "dheater"
categories:
  - dos
homepage: "https://github.com/c0r0n3r/dheater"
github-repo: "c0r0n3r/dheater"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: Apache-2.0
github-stars: 224
github-pushed: "2026-08-04"
gh-checked: "2026-11-14"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# dheater

> Proof-of-concept for the D(HE)at attack (CVE-2002-20001) — exhausts a server's CPU by forcing expensive Diffie-Hellman key exchanges over TLS or SSH.

## What it's for

D(HE)ater is the reference implementation of the D(HE)at denial-of-service class: a client can deliberately choose DHE or ECDHE key exchange, and public servers then perform the costly modular-exponentiation work before any authentication happens. The tool opens many such handshakes (optionally in parallel threads) and saturates the target's CPU without sending much traffic — a resource-exhaustion test for TLS and SSH endpoints. It is defensive-oriented: use it to verify that rate limiting, key-exchange restrictions, or crypto-provider mitigations actually hold. The installed command is `dheat`; the project moved from the Balasys GitHub org to `c0r0n3r` (the CryptoLyzer author), with development mirrored on GitLab at dheatattack/dheater and releases published to PyPI as `dheater`.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Saturate a TLS service with DHE handshakes

```
dheat --protocol tls example.com:443
```

Outcome: continuously initiates finite-field DHE handshakes against the target; watch the server's CPU/Load while it runs — a vulnerable configuration shows crypto load climbing from trivial client traffic.

### Test an SSH endpoint

```
dheat --protocol ssh example.com:22
```

Outcome: same CPU-exhaustion pattern against SSH's DH group exchange; a mitigation that caps unauthenticated DH work shows flat load.

### Force elliptic-curve exchange instead

```
dheat --protocol tls --key-exchange ECDHE example.com:443
```

Outcome: enforces ECDHE rather than DHE — targets that only mitigated finite-field groups get stressed through their curve implementation.

### Parallelize the load

```
dheat --protocol tls --thread-num 16 --timeout 10 example.com:443
```

Outcome: 16 concurrent worker threads with 10-second socket timeouts — the standard shape for load-testing a mitigation at scale; `--key-size` can additionally pin the group size where the server permits it.
