---
name: "tls-prober"
categories:
  - blackarch-fingerprint
version: "286.72b1029"
homepage: "https://github.com/WestpointLtd/tls_prober"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "WestpointLtd/tls_prober"
github-stars: 265
github-pushed: "2021-11-13"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tls-prober

> Westpoint's TLS Prober: identifies the SSL/TLS *implementation* behind a server (OpenSSL, SChannel, Java JSSE, GnuTLS, wolfSSL, mbedTLS, MatrixSSL…) from its behavioural responses rather than its cipher list or certificate.

## What it's for

Answering "what TLS stack is this server actually running" — useful when the banner, certificate and cipher list tell you nothing, or when you need the implementation and build to reason about stack-specific quirks and vulnerabilities. Written in Python 2 by Richard Moore (BlackArch wraps `prober.py` as the `tls-prober` command), it fires a series of deliberately malformed and edge-case TLS probes, compares the responses against a signature database, and prints the best-matching implementations with scores. Because it keys on behaviour rather than supported ciphers (which administrators routinely change), it stays reliable against reconfigured servers, and it distinguishes builds of the same library. It speaks plain TLS as well as STARTTLS protocols (SMTP, FTP, POP3, IMAP, with auto-detection), and can record new signatures for the database. It identifies implementations, not specific software products, and cannot tell you anything about a server it cannot reach.

## Typical use cases

### Identify the TLS implementation of a server

Fingerprint the TLS stack on the standard HTTPS port.

```
tls-prober target.example.com
```

Outcome: probes port 443 and prints a ranked list of matching signatures with scores, best first — e.g. `openssl-1.0.1h default source build 15` — telling you the library and build behind the endpoint.

### Probe a nonstandard port

Fingerprint a TLS service that is not on 443.

```
tls-prober -p 8443 target.example.com
```

Outcome: runs the same probe set against the specified port and prints the ranked implementation matches for whatever answers there.

### Fingerprint a STARTTLS mail service

Identify the TLS stack of a service that only upgrades from plaintext.

```
tls-prober -s smtp -p 25 mail.example.com
```

Outcome: connects to port 25, issues the SMTP STARTTLS upgrade before probing, and prints the ranked matches; other modes are `ftp`, `pop3`, `imap` and `none`, with `auto` (the default) detecting the protocol itself.

### Record a signature for a new implementation

Teach the database about an unknown TLS stack.

```
tls-prober -a 'ACME TLS 1.0.0' target.example.com
```

Outcome: probes the server and adds its signature to the local fingerprint database under the given name, so future scans identify it; signatures can be submitted upstream for inclusion in releases. `-n` forces all probes to run instead of the quick scan.

*No corpus evidence — derived from upstream documentation.*
