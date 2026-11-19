---
name: "cipherscan"
categories:
  - blackarch-scanner
version: "423.30bd239"
homepage: "https://github.com/jvehent/cipherscan"
packaged-by: [blackarch]
blackarch-builddate: "2025-06-14"
build-system: scripts
license: OpenSSL
meta-checked: "2026-11-09"
github-repo: "jvehent/cipherscan"
github-stars: 1991
github-pushed: "2025-06-09"
github-archived: false
github-issues: 37
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cipherscan

> Quick SSL/TLS capability probe: full cipher-suite ordering, curves, PFS, certificate and OCSP details for any endpoint.

## What it's for

cipherscan (original author Julien Vehent) determines which SSL/TLS cipher suites a target accepts and in what preference order across protocol versions, plus elliptic curves, forward-secret key exchange, certificate details, TLS ticket lifetime, OCSP stapling and who controls cipher ordering. It is a wrapper around `openssl s_client` and ships a patched OpenSSL build so it can offer ciphers a stock system OpenSSL lacks (point it at your own binary with `-o`). The companion `analyze.py` grades a target against the Mozilla server-side TLS guidelines (old/intermediate/modern) and can act as a Nagios check. It probes listening servers only — it cannot test client-side TLS behavior.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Fingerprint a TLS endpoint

Capture everything an HTTPS listener will negotiate.

```
cipherscan example.com
```

Outcome: prints the accepted cipher suites in priority order with protocols, PFS group and curves per cipher, then certificate trust/size/signature, ticket lifetime hint, OCSP stapling support and whether the server enforces cipher ordering.

### Include elliptic-curve preferences

Also enumerate curve support and ordering.

```
cipherscan --curves example.com
```

Outcome: the same report plus per-cipher curve lists and the target's curves ordering and fallback behavior.

### Test a STARTTLS service

Probe services that wrap TLS after a protocol handshake.

```
cipherscan --curves -starttls xmpp jabber.example.com:5222
```

Outcome: cipherscan negotiates the given protocol's STARTTLS handshake before testing, producing the same cipher/curve report as for a plain TLS endpoint.

### Machine-readable output

Emit JSON for tooling and diffing.

```
cipherscan -j example.com > cipherscan.json
```

Outcome: one JSON object with target, timestamp, per-cipher details (protocols, PFS, pubkey size, signature algorithm, curves, OCSP) and the serverside-ordering flag — suitable for tracking a configuration over time.

### Grade against Mozilla TLS guidelines

Get a configuration level and the deltas needed to improve it.

```
analyze.py -t example.com
analyze.py --nagios -t example.com
```

Outcome: the companion script (shipped with cipherscan) names the Mozilla level the target matches — old, intermediate or modern — and lists the exact changes needed to reach each level; with `--nagios` the exit code maps to ok/warning/critical for monitoring use.
