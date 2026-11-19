---
name: "sslcaudit"
categories:
  - blackarch-scanner
version: "524.f218b9b"
homepage: "https://github.com/grwl/sslcaudit"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "grwl/sslcaudit"
github-stars: 29
github-pushed: "2016-02-11"
github-archived: false
github-issues: 23
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sslcaudit

> Audits SSL/TLS clients, not servers: listens for a connection, presents forged certificates in turn, and records which MITM tricks the client accepts.

## What it's for

sslcaudit (Alexandre Bezroutchko/gremwell, Python 2 + M2Crypto, GPLv3) automates security audits of SSL/TLS clients — thick client applications, mobile apps, appliances, anything speaking TLS over TCP. It listens for an incoming connection and runs the `sslcert` module: in sequence it presents a self-signed certificate, one with the wrong CN, one signed by a user-supplied CA and variants, then prints a table of which certificate attacks the client accepted. Any acceptance is a man-in-the-middle weakness — the client is not validating who it talks to. Testing servers (cipher/protocol scans) is out of scope; use a server-side scanner for that. Default behavior quits after the first client has been processed.

## Typical use cases

### Test a client's certificate validation

```
sslcaudit
```

Outcome: listens on `0.0.0.0:8443` with the default `sslcert` module; point the client under test at that host and port. Once the client connects and the sequence completes, the run finishes with a per-attack table — self-signed, wrong-CN, user-CA-signed and friends — where every non-rejected attack is a reported MITM weakness.

### Audit several clients in a row

```
sslcaudit -l 0.0.0.0:8443 -a repeat
```

Outcome: keeps the listener up and repeats the full certificate-attack sequence for every client that connects instead of exiting after the first — for testing multiple application instances or reconnecting clients.

### Self-test the installation

```
sslcaudit -T 2
```

Outcome: runs the audit against sslcaudit's own built-in test client (1 = plain TCP client, 2 = CN-verifying client, 3 = curl with a user CA, requiring `--user-ca-cert/--user-ca-key`) and prints the attack table — verifies the toolchain works and shows what a properly validating client's results look like.

*No corpus evidence — derived from upstream documentation (README and option parser source).*
