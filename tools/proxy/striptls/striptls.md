---
name: "striptls"
categories:
  - blackarch-proxy
version: "55.5ec712c"
homepage: "https://github.com/tintinweb/striptls"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "tintinweb/striptls"
github-stars: 168
github-pushed: "2021-11-25"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# striptls

> Python 2 auditing proxy that intercepts TLS/STARTTLS sessions and tests whether clients survive STARTTLS stripping.

## What it's for

striptls sits between a client and a server and mangles the TLS upgrade negotiation. SMTP, POP3, IMAP, FTP, NNTP, XMPP, ACAP and IRC each have dedicated vectors: strip STARTTLS from the server's capability list, answer the client's STARTTLS with an error or an invalid response code, drop it silently, or intercept the upgraded session with an untrusted certificate. In the default audit mode it iterates every applicable vector against each connecting client and reports which downgrade tricks that client tolerated — i.e. whether it continued in plaintext instead of aborting. It can also act as a protocol-independent TLS interception proxy for implicit-TLS services (`GENERIC.Intercept` / `--generic-ssl-intercept`) or as a plain TCP relay (`--vectors NONE`). Python-2-only (needs 2.7.9+ for `SSLContext`); the interception vectors require a PEM file containing certificate plus private key (`server.pem` by default, `-k` to point elsewhere).

## Typical use cases

### Audit a client's STARTTLS downgrade behavior

Point the client at the proxy and let it run every vector.

```
striptls --listen 127.0.0.1:8825 --remote mail.example.com:25
```

Outcome: all protocol vectors are registered at startup; as the client connects and talks, each stripping trick is applied in turn with the mangled protocol exchange logged line by line. Press Ctrl+C to abort and get the per-client audit table marking each vector `[Vulnerable!]` or not.

### Strip STARTTLS from the server's capabilities

Make the client believe the server offers no TLS at all.

```
striptls --listen 0.0.0.0:8825 --remote mail.example.com:25 --vectors SMTP.StripFromCapabilities
```

Outcome: the server's EHLO response is rewritten on the fly to remove the STARTTLS capability; a client that proceeds to send mail in plaintext has accepted the downgrade, one that refuses it is resilient against this vector.

### Intercept the session with an untrusted certificate

MITM clients that do not validate the server certificate after STARTTLS.

```
striptls --listen 0.0.0.0:8825 --remote mail.example.com:25 --vectors SMTP.UntrustedIntercept
```

Outcome: the proxy answers STARTTLS itself with the certificate from `server.pem` (single PEM with cert and private key, expected in the working directory) and relays the session in the clear toward you; a client that completes the handshake without validating the cert keeps talking "encrypted" while every command is logged.

### Intercept implicit TLS without STARTTLS

For services that are TLS from the first byte, use generic handshake detection.

```
striptls --listen 0.0.0.0:9999 --remote mail.example.com:465 --vectors GENERIC.Intercept
```

Outcome: the proxy peeks at the first bytes of each connection, detects the SSL/TLS handshake, and terminates TLS on both sides — a protocol-independent TLS-to-TLS interception relay that logs the decrypted application traffic.

### Use as a plain TCP relay

No mangling, just forwarding and observation.

```
striptls --listen 0.0.0.0:9999 --remote mail.example.com:25 --vectors NONE
```

Outcome: sessions are relayed verbatim between the two ends; useful as a base to watch protocol chatter, or combined with `--generic-ssl-intercept` to convert only the TLS parts of a session.

*No corpus evidence — derived from upstream documentation.*
