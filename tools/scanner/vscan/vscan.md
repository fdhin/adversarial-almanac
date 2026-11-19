---
name: "vscan"
categories:
  - blackarch-scanner
version: "10.da4e47e"
homepage: "https://github.com/pasjtene/Vscan"
packaged-by: [blackarch]
blackarch-builddate: "2018-09-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "pasjtene/Vscan"
github-stars: 2
github-pushed: "2016-03-13"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vscan

> Java HTTPS profiler — walks a target through SSLv3 to TLSv1.2 and every cipher suite, reporting what is accepted and which weak ciphers map to CVEs.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Mapping an HTTPS endpoint's TLS landscape. Hand vscan an `https://` URL and it deliberately trusts any certificate (self-signed and expired included — it was built for lab testing), then for each protocol version it supports (SSLv3, TLSv1, TLSv1.1, TLSv1.2) tries every cipher suite the JVM offers, records which connections succeed, prints the accepted-cipher list per protocol, and feeds it through a bundled vulnerability database that names the CVEs behind weak ciphers with remediation hints. A Java NetBeans project by Pascal Tene; BlackArch wraps Vscan.jar as the `vscan` command. Note the blind spots implied by its age: no TLS 1.3, and its cipher reach is bounded by the local Java runtime — the source explains how to re-enable the JDK's disabled algorithms for fuller coverage.

## Typical use cases

### Profile accepted protocols and ciphers

```
vscan https://target.example.com
```

Outcome: prints a "Testing with protocol SSLv3/TLSv1/TLSv1.1/TLSv1.2" section per protocol; inside each, one "Connecting with <cipher>: Response Code/Success" line per attempted suite, then the accepted-cipher list with its count, and finally a vulnerabilities section mapping the weak ciphers to CVEs with workaround notes.
