---
name: "starttls-mitm"
categories:
  - blackarch-proxy
version: "7.b257756"
homepage: "https://github.com/ipopov/starttls-mitm"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: APACHE
meta-checked: "2026-11-09"
github-repo: "ipopov/starttls-mitm"
github-stars: 51
github-pushed: "2014-03-18"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# starttls-mitm

> STARTTLS-aware MITM relay that dumps both plaintext and decrypted TLS traffic for XMPP-style protocols.

## What it's for

starttls-mitm (command `mitm_listener`) is a small Python 2 man-in-the-middle relay for protocols that start in plaintext and upgrade to TLS via STARTTLS. It relays both directions, peeks at each client packet for a TLS ClientHello, and at that moment wraps both legs of the connection with a user-provided key/certificate — impersonating the remote server to the client — while printing all traffic (`C->S` and `S->C` lines) to stdout, so you capture both the plaintext handshake phase and the decrypted TLS session. The listener and upstream port are hardcoded to 5222 (XMPP), and it has only been tested against XMPP, though the approach should carry over to IMAP and other STARTTLS protocols. The intercepted device must be explicitly configured to trust your certificate authority, so this is an interception you set up, not a break of TLS.

## Typical use cases

### Intercept an XMPP session with its STARTTLS upgrade

Point a test XMPP client at the listener and capture its traffic in the clear.

```
openssl req -x509 -newkey rsa:2048 -keyout ca.key -out ca.crt -days 30 -nodes
mitm_listener target.example.com ca.key ca.crt
```

Outcome: openssl creates the self-signed keypair, then the listener binds 0.0.0.0:5222 and relays connections to target.example.com:5222, printing `CLIENT CONNECT` and `C->S`/`S->C` payload lines to stdout. When the client begins its TLS handshake the relay terminates TLS with ca.crt on the client side and re-establishes TLS upstream, so the printed payloads remain readable plaintext. Trust ca.crt in the test client (whose server address must point at the interceptor) for the upgraded leg to complete.

*No corpus evidence — derived from upstream documentation.*
