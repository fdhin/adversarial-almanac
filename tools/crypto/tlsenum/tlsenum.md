---
name: "tlsenum"
categories:
  - blackarch-crypto
version: "78.787c88b"
homepage: "https://github.com/Ayrx/tlsenum"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Ayrx/tlsenum"
github-stars: 35
github-pushed: "2017-04-25"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tlsenum

> Minimal TLS enumeration tool that reports which protocol versions and cipher suites a server accepts, listed in the server's own preference order.

## What it's for

tlsenum sends TLS ClientHello messages and parses the ServerHello replies to work out which protocol versions (including an SSL 2.0 probe) and cipher suites a service supports, printing the suites in the order the server selects them. It assumes server-side cipher preference — the server picks its favorite suite regardless of client order — which most implementations follow, so the output approximates the server's real negotiation order rather than just an unordered capability list. The tool is deliberately bare: it takes a host and a port, has no other options, and reports versions, compression support, and the cipher list — no scoring, no certificate inspection, no protocol-attack tests. Upstream was archived in 2017 and predates TLS 1.3, so use it for legacy services or reach for a maintained enumerator otherwise.

## Typical use cases

### Enumerate a server's TLS versions and cipher-suite preference order

Ask a service which TLS versions and ciphers it will actually negotiate.

```
tlsenum target.example.com 443
```

Outcome: performs repeated handshakes, then prints the TLS versions the server accepted (e.g. `TLS Versions supported by server: 3.0, 1.0, 1.1, 1.2`), whether Deflate compression is offered, and the supported cipher suites in priority order; the ordered list is the part you quote when reporting weak-suite preference or obsolete protocol versions on a service.

*No corpus evidence — derived from upstream documentation.*
