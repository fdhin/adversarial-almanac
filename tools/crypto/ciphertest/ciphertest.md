---
name: "ciphertest"
categories:
  - blackarch-crypto
version: "22.e33eb4a"
homepage: "https://github.com/OpenSecurityResearch/ciphertest"
packaged-by: [blackarch]
blackarch-builddate: "2017-05-18"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "OpenSecurityResearch/ciphertest"
github-stars: 28
github-pushed: "2017-05-17"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ciphertest

> SSL/TLS cipher-suite enumerator driven by gnutls-cli — probes a host with up to ~3,200 protocol/cipher/MAC/key-exchange combinations and annotates the weak ones.

## What it's for

ciphertest (`cipherTest.sh`, installed as `ciphertest`) answers "which SSL/TLS cipher suites does this service actually accept, including the ancient ones?" It drives `gnutls-cli`, which supports many more protocol/cipher/MAC/key-exchange combinations than openssl-based checkers, covering SSLv2 through TLS 1.2 era configurations — around 3,200 candidate combinations, pre-optimized to skip known-impossible pairings. It reads the target's accepted suites and flags weaknesses inline (MD5 MACs, SSL 3.0 with POODLE, short export keys), which is what you need for a compliance-style audit or for documenting that a legacy service still negotiates obsolete crypto. It enumerates cipher support only; it does not test for protocol-level attacks, parse certificates, or rate the host beyond the cipher table.

## Typical use cases

### Enumerate accepted cipher suites on a TLS service

```
ciphertest target.example.com 443
```

Outcome: resolves the host, then systematically attempts each protocol/cipher/MAC/key-exchange combination with gnutls-cli and prints the accepted set as a table of `Proto / Cipher / MAC / KeX` rows (SSLv2 rows use openssl), with weak entries annotated — `Weak MAC` for MD5, `Weak proto (POODLE)` for SSL 3.0, `Short key` for 40-bit export ciphers. The resulting table is the host's effective cipher posture, ready to quote in an assessment finding.

### Watch the probe run in detail on a slow or filtered host

```
DEBUG=3 ciphertest target.example.com 8443
```

Outcome: prints each individual test with a live `Supported`/`Failed` verdict and a progress counter instead of just the optimizing spinner, so you can see which combinations hang, which fail fast, and how far along the sweep is. Use this when the default run seems stalled (packet-filtered port, IDS interaction) or when you want evidence of exactly which combos were tried. Any port works — point it at 8443, 4443, or a TLS-wrapped service on a nonstandard port.

*No corpus evidence — derived from upstream documentation.*
