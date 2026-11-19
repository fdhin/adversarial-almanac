---
name: "tls-map"
categories:
  - blackarch-crypto
version: "v3.4.0.r2.g2051cf4"
homepage: "https://noraj.github.io/tls-map/"
packaged-by: [blackarch]
blackarch-builddate: "2026-11-01"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "noraj/tls-map"
github-stars: 12
github-pushed: "2026-11-08"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 58
gh-activity-checked: "2026-11-09"
---

# tls-map

> Offline translator between TLS cipher-suite naming schemes — IANA, OpenSSL, GnuTLS, NSS, or hex codepoints — with security-level/vulnerability metadata and parsers for other scanners' output files.

## What it's for

Every TLS tool names cipher suites differently, and tls-map closes that gap: give it a name in any of the four major vocabularies (or a hex codepoint) and it prints the equivalents, fully offline from a bundled, integrity-checked database. Beyond translation, `search --extended` adds protocol versions, a security level, and known vulnerabilities for a suite; `bulk` and `extract` modes map and audit cipher lists taken from other tools' output files (SSLyze, sslscan2, testssl.sh, ssllabs-scan, tlsx), flagging weak suites; and `export` dumps the complete mapping as markdown, JSON, or Ruby marshal. Use it when post-processing scanner output, normalizing cipher names across tools in a report, or deciding whether a suite observed in a handshake is considered weak. It is purely a lookup and translation layer — it never talks to a server itself.

## Typical use cases

### Translate a cipher name between naming schemes

Convert an OpenSSL-style name to all other vocabularies.

```
tls-map search openssl AES128-SHA
```

Outcome: prints the codepoint and the suite's name in every supported vocabulary (IANA, OpenSSL, GnuTLS, NSS) for the OpenSSL-style input, working offline from the bundled database; `No match found` means the term is unknown in the given vocabulary (accepted criteria: codepoint, iana, openssl, gnutls, nss).

### Look up the security level and vulnerabilities of a suite

Get a citable reason a cipher is considered weak.

```
tls-map search iana TLS_RSA_WITH_AES_128_CBC_SHA --extended
```

Outcome: after the translation table, fetches (online) extended details — which TLS versions the suite applies to, a color-coded security level, and any known vulnerabilities with severity and description — turning a cipher name into report-ready justification.

### Audit cipher suites extracted from a scanner's output file

Turn existing scan data into a weak-cipher findings list.

```
tls-map extract scan.json sslscan2 --only-weak
```

Outcome: parses the sslscan2 output file, extracts every cipher suite it reports, and prints only those with security level weak or insecure; accepted input formats are sslyze4, sslyze6, sslscan2, testssl, ssllabs-scan, and tlsx, so most scan archives can be re-audited without re-scanning.

### Export the full cipher mapping table

Materialize the whole database for reuse.

```
tls-map export ciphers.md markdown
```

Outcome: writes ciphers.md containing the complete cipher-suite mapping (codepoints plus all four vocabularies) as a markdown table; other formats are json_pretty, json_compact, and marshal for loading directly in Ruby.

*No corpus evidence — derived from upstream documentation.*
