---
name: "sslmap"
categories:
  - blackarch-scanner
version: "0.2.0"
homepage: "http://thesprawl.org/projects/latest/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "iphelix/sslmap"
github-stars: 65
github-pushed: "2019-06-11"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sslmap

> Lightweight TLS/SSL cipher-suite scanner: probes an endpoint for every known cipher and rates what it accepts.

## What it's for

sslmap (iphelix, Python 2, packaged from `sslmap-0.2.0.py` published at thesprawl.org) is a lightweight TLS/SSL cipher suite scanner designed to give a simple, reliable read on weak ciphers enabled on an SSL endpoint. It uses its own SSL engine rather than the local library, so it can detect uncommon cipher suites (e.g. GOST) that other scanners cannot offer at all, and it rates accepted suites into LOW/MEDIUM/HIGH strength classes in a summary table. Protocol versions are selected per run; without an explicit flag it tests TLS v1.0 and SSL v3.0. It scans one host and port per invocation — it is not a range scanner and does not grade the certificate chain.

## Typical use cases

### Scan a web endpoint's cipher suites

```
sslmap --host www.example.com
```

Outcome: probes port 443 for all 229 known cipher suites over the default TLS v1.0 and SSL v3.0 handshakes, printing each accepted suite, then a `Scan Results` summary grouping the accepted suites by strength rating — export-grade and RC4/DES entries in the LOW group are the findings to chase.

### Target another port and protocol generation

```
sslmap --host mail.example.com --port 993 --tls12
```

Outcome: same cipher sweep against the IMAPS port restricted to TLS v1.2 handshakes — confirms whether a service still negotiating old protocols is doing so because nothing newer is enabled (`--ssl3`, `--tls11` select other versions; several flags combine).

### Fuzz unknown cipher IDs

```
sslmap --host www.example.com --fuzz
```

Outcome: walks all possible cipher-suite IDs instead of only the known list, reporting any suite the server accepts that is not in the public table — the way uncommon or vendor-specific suites (e.g. GOST) surface; slow by design.

*No corpus evidence — derived from upstream documentation (README and script source).*
