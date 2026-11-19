---
name: "tlssled"
categories:
  - blackarch-automation
version: "1.3"
homepage: "http://blog.taddong.com/2011/05/tlssled-v10.html"
packaged-by: [blackarch, kali]
blackarch-builddate: "2016-12-31"
meta-checked: "2026-11-09"
github-repo: "dinosec/TLSSLed"
github-stars: 2
github-pushed: "2015-11-11"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tlssled

> SSL/TLS configuration check battery for HTTPS servers, built on sslscan and openssl.

## What it's for

TLSSLed (Raul Siles, Taddong) is a shell script that wraps `sslscan` (run
once, output cached to a local log to spare the target) and `openssl
s_client` into a single assessment of an HTTPS implementation: SSLv2
support, the NULL cipher, weak 40/56-bit ciphers, availability of strong
ciphers such as AES, MD5-signed certificates, and insecure renegotiation
(CVE-2009-3555). Each check prints one line marked `[-]` (not vulnerable),
`[+]` (finding), `[.]` (informational) or `[!]` (warning/error), so a run
can be skimmed fast. It is a 2013-era tool: its protocol view stops at the
TLS of that period, so for modern TLS 1.2/1.3 deployments use a maintained
scanner (e.g. testssl.sh) instead.

## Typical use cases

### Audit an HTTPS server

```
tlssled target.example.com 443
```

Outcome: runs the full check battery against the target and prints each
result line; all artifacts (the sslscan log and openssl transcripts) are
written under a `TLSSLed_1.3_<host>_<port>_<timestamp>` output directory
for later review.

### Audit a TLS service on a non-standard port

```
tlssled 198.51.100.10 8443
```

Outcome: the same battery against the TLS service listening on 8443 — any
`<host> <port>` pair that speaks SSL/TLS works, not only HTTPS on 443.

*No corpus evidence — derived from upstream documentation.*
