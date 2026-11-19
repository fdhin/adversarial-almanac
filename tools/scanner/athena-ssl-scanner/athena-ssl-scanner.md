---
name: "athena-ssl-scanner"
categories:
  - blackarch-scanner
version: "0.6.2"
homepage: "http://packetstormsecurity.com/files/93062/Athena-SSL-Cipher-Scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# athena-ssl-scanner

> Java SSL/TLS cipher-support scanner that walks an endpoint through roughly 150 SSLv2/SSLv3/TLSv1 cipher suites and reports which ones the service accepts, with a matching client-side mode.

*No corpus evidence — derived from upstream documentation (the packaged 0.6.2 release, executed locally to verify flags).*

## What it's for

Finding out which ciphers an SSL/TLS service actually accepts, which is the raw material for judging weak-suite exposure (legacy SSLv2/v3, export and NULL ciphers) on web, mail and directory services. Written by Darren McDonald (v0.6.2, GPL3, published via Packet Storm); the BlackArch package wraps the jar as `athena-ssl-scanner`. It is an enumerator, not a vulnerability scanner — it reports the accepted cipher set (and optionally which ciphers should be disabled), leaving CVE interpretation to you. A `--client` mode inverts the test to enumerate the ciphers a client will use.

## Typical use cases

### Enumerate the ciphers a service accepts

```
athena-ssl-scanner 198.51.100.10 443
```

Outcome: connects to the host and offers its cipher list suite by suite, printing each cipher the service accepts. The accepted set is the review baseline; anything SSLv2/v3 or export-grade in it is a finding. Equivalent raw form: `java -jar athena-ssl-cipher-check.jar 198.51.100.10 443`.

### Retry a non-conformant service in safe mode

```
athena-ssl-scanner --safe --timeout 10000 198.51.100.10 443
```

Outcome: sends cipher lists in smaller chunks instead of large batches. If a normal scan returns no SSLv3/TLS ciphers at all, the implementation probably mishandles big lists — safe mode trades speed for complete results. `--timeout` raises the per-request wait (default 5000 ms).

### Check which ciphers a client uses

```
athena-ssl-scanner --client 8443
```

Outcome: starts a local listener on the port; connect an SSL client (browser, mail client) to this host and port, and the tool records the ciphers that client negotiates. All other options are ignored in client mode — useful for fingerprinting outbound TLS posture.

### Get a triage view of what to disable

```
athena-ssl-scanner --lazy 198.51.100.10 443
```

Outcome: after the scan, lists the accepted ciphers that should be disabled, grouped by issue class, per the author's judgement ("Lazy Consultant Mode") — a quick cut of the full cipher dump for reporting.
