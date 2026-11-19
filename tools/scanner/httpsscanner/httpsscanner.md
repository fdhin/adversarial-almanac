---
name: "httpsscanner"
categories:
  - blackarch-scanner
version: "1.2"
homepage: "https://code.google.com/p/libre-tools/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-15"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# httpsscanner

> Java cipher-strength auditor for SSL/TLS web servers: connects with one ciphersuite at a time, from null and anonymous suites up to strong ones, and reports exactly where the server stops refusing.

*No corpus evidence — derived from upstream documentation (httpsScanner 1.1 source extracted from the packaged httpsScanner.jar; usage string read from its `main()`).*

## What it's for

httpsScanner (chan fook sheng, libre-tools, 2009) is a single-purpose TLS configuration checker. Given a host, port, HTTP method and URL path, it opens a series of SSL connections, each negotiating exactly one ciphersuite, walking four groups in order: no-encryption NULL ciphers (`SSL_RSA_WITH_NULL_*`), anonymous Diffie-Hellman suites (vulnerable to man-in-the-middle), weak export/legacy ciphers, and finally strong ones. For each suite it reports `Connection successful` when the handshake completes, and ends with per-group counts. Any "connection successful" line in the null or anonymous groups is a finding: the server is configured to accept suites that provide no confidentiality or no authentication. It predates modern TLS (SSLv3/TLS1.0-era suite names) and audits configuration only — it is not a vulnerability scanner, and a modern alternative such as `testssl.sh` or `sslyze` will give a more current picture.

## Typical use cases

### Audit a TLS endpoint's weakest accepted ciphersuite

```
httpsscanner target.example.com 443 HEAD /
```

Outcome: Walks the null, anonymous, weak and strong ciphersuite groups one suite at a time, printing each attempted suite and whether the connection succeeded; the summary counts show whether any no-security or anonymous suite was accepted — those must be disabled server-side.

### Check a service on an alternate port and path

```
httpsscanner target.example.com 8443 GET /login
```

Outcome: Same suite-by-suite walk against the TLS service on port 8443, issuing `GET /login` over each successful connection so the check exercises a real application path rather than the root.
