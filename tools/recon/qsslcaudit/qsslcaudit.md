---
name: "qsslcaudit"
categories:
  - information-gathering
  - web
version: "0.8.3-0kali2"
homepage: "https://github.com/gremwell/qsslcaudit"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: cmake
meta-checked: "2026-11-09"
github-repo: "gremwell/qsslcaudit"
github-stars: 28
github-pushed: "2026-05-15"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# qsslcaudit

> Audits TLS/SSL *clients* instead of servers: it impersonates a TLS endpoint and reports whether the connecting application accepts bad certificates, weak protocols and ciphers.

## What it's for

Most TLS scanners grade servers; qsslcaudit (a C++/Qt rewrite of sslcaudit) grades the client. It listens for a TLS connection and, per test, presents a deliberately flawed certificate or protocol choice — self-signed, wrong hostname, untrusted CA, SSLv2/SSLv3, EXPORT/LOW ciphers — then reports exactly what the client accepted, answering questions like "does this app actually verify certificates?" (the difference between an interception-capable client and a safe one). You must route the client's traffic to the listener: change the app's server setting, edit the client's hosts file, DNAT port 443 to the listener, or use a forwarding proxy. Tests are numbered; `qsslcaudit -h` lists them and the supported `--starttls` protocols.

## Typical use cases

### Run the full client-certificate audit

Default all-tests listener.

```
qsslcaudit
```

Outcome: listens on 127.0.0.1:8443; point the client at it and for each connection it cycles through the selected tests, printing per-test whether the TLS handshake succeeded — a successful handshake with a bogus certificate means the client is interception-vulnerable.

### Test self-signed certificate acceptance

The single most common client flaw.

```
qsslcaudit --selected-tests 2 --user-cn login.example.com
```

Outcome: presents a self-signed certificate for the given common name and reports whether the client completed the handshake — acceptance means a machine-in-the-middle attacker can serve any certificate.

### Present a valid certificate for the wrong domain

Test hostname verification with a real certificate.

```
qsslcaudit --selected-tests 1 --user-cert cert-chain.pem --user-key key.pem -l 0.0.0.0
```

Outcome: serves a legitimate, chained certificate you own (for a different name than the client expects) and reports acceptance — a pass here indicates the client ignores the certificate's CN/SAN. `-l 0.0.0.0` opens the listener to external clients.

### Mirror the real server's certificate settings

Reproduce the genuine endpoint's TLS profile.

```
qsslcaudit --server example.com
```

Outcome: fetches the certificate served by the given host and presents equivalent settings to the client, isolating certificate-chain trust from other handshake differences.

### Audit STARTTLS-capable clients

Test mail and other plaintext-then-TLS protocols.

```
qsslcaudit --starttls smtp
```

Outcome: performs the chosen STARTTLS handshake sequence (protocols listed in `-h` output) before offering TLS, letting you audit SMTP/IMAP/FTP-style clients that upgrade rather than connect TLS-first.

### Keep testing across repeated connections

Run a client battery or scanner against the listener.

```
qsslcaudit --loop-tests
```

Outcome: re-runs the test cycle for every incoming connection instead of once — use when the client reconnects repeatedly or when pointing other assessment tools at the listener; `--show-ciphers` prints the ciphers the linked OpenSSL supports.

No corpus evidence — derived from upstream documentation.
