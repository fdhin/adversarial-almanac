---
name: "crlf-injector"
categories:
  - blackarch-fuzzer
version: "9.bd6db06"
homepage: "https://github.com/rudSarkar/crlf-injector"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "rudSarkar/crlf-injector"
github-stars: 49
github-pushed: "2022-04-08"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crlf-injector

> A bulk CRLF-injection tester for lists of hostnames, by Rudra Sarkar.

## What it's for

crlf-injector mass-checks web hosts for CRLF injection. For every hostname in
a list it requests `http://<host><payload>` with a URL-encoded CRLF payload
and inspects the response: if the injected header line
(`evil-here: malicious_cookie1`) comes back reflected in the server's
response headers, the host is injectable. It is a minimal Python 2 script
built on `requests`, takes exactly two arguments (list file and payload), and
tests plain HTTP only — no HTTPS, no custom headers or methods, no
response-splitting payload variations beyond what you pass in.

## Typical use cases

### Show usage and the built-in payload suggestions

```
crlf-injector
```

Outcome: prints the banner, the usage line (`crlf.py domain_list.ext
crlf_payload`) and the two suggested payloads `/%0aevil-here:malicious_cookie1`
and `/%0d%0aevil-here:malicious_cookie1`, then exits.

### Scan a list of hosts for CRLF injection

```
crlf-injector hosts.txt '/%0d%0aevil-here:malicious_cookie1'
```

Outcome: prints `[+] Scanning Start For CRLF Injection`, then for every
hostname in `hosts.txt` (one per line) shows the tested URL and its status
code; a host that reflects the injected `evil-here` header prints the header
value and is CRLF-injectable, otherwise `[!] Not vulnerable !!!!`, and
unreachable hosts print `[!] Website offline`. Note the tool assumes the
reflected `evil-here` header as its success signal, so payloads that inject a
different header name will always report not vulnerable.

*No corpus evidence — derived from upstream documentation.*
