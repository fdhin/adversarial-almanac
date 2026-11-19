---
name: "uff"
categories:
  - blackarch-fuzzer
version: "46.93fea53"
homepage: "https://github.com/sw33tLie/uff"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-10"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "sw33tLie/uff"
github-stars: 251
github-pushed: "2025-10-29"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# uff

> "Unleashed ffuf" — a drop-in ffuf fork by sw33tLie whose modified HTTP stack can send requests stock ffuf cannot.

## What it's for

uff is a web content fuzzer forked from ffuf (currently based on ffuf 2.10-dev) that keeps full ffuf syntax while replacing the Go net/http and net/url layers, so raw, non-conformant and technically invalid HTTP becomes sendable: absolute-URI request lines (`-opaque`), headers without canonical capitalization, malformed headers (leading spaces, missing colon), bodies without a Content-Length header (`-no-content-length`), and genuinely raw request fuzzing (`--method-as-raw-request`). Its defaults differ from ffuf — a current-Chrome user agent and 200 threads — and `-request` replays a file byte-for-byte instead of reordering it. Any ffuf wordlist-driven discovery workflow works unchanged; uff adds the protocol-edge and WAF-evasion cases on top.

## Typical use cases

### Fuzz through an absolute-URI request line

```
echo hi | uff -c -u http://example.com -w - -opaque "http://198.51.100.10/FUZZ"
```

Outcome: sends each wordlist entry in absolute-URI form (`GET http://198.51.100.10/hi HTTP/1.1`), a request shape ffuf cannot express — useful against proxies and virtual-host routers; the wordlist is read from stdin.

### Send malformed and non-canonicalized headers

```
echo hi | uff -c -u http://example.com/FUZZ -w - -H '   I AM AN INVALID: HEADER'
```

Outcome: delivers the header verbatim — leading whitespace intact, no automatic header canonicalization — letting you test parser and WAF assumptions that well-behaved clients normalize away; lowercase header names pass through unchanged the same way.

### Fuzz a fully raw request

```
echo hi | uff -u http://example.com/ --method-as-raw-request -w - -c -X $'GET /FUZZ HTTP/0.9\r\nHost: {HOST}\r\n\r\n'
```

Outcome: the entire request (here an HTTP/0.9 request line) is carried inside the method field and emitted untouched, with `{HOST}` substituted automatically — raw-request fuzzing in the manner of Burp Suite or Caido, including the ability to fuzz the HTTP version itself.

### Replay a request file byte-for-byte

```
echo hi | uff -c -request req.txt -u http://example.com -w -
```

Outcome: sends `req.txt` exactly as written — no header reordering, nothing RFC-non-compliant dropped — with FUZZ keywords substituted from the wordlist; unlike ffuf, `-u` is mandatory here because the destination cannot be inferred from the file alone.

*No corpus evidence — derived from upstream documentation.*
