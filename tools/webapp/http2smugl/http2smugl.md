---
name: "http2smugl"
categories:
  - blackarch-webapp
version: "43.ccdf772"
homepage: "https://github.com/neex/http2smugl"
packaged-by: [blackarch]
blackarch-builddate: "2025-03-29"
build-system: go
license: Unlicense
meta-checked: "2026-11-09"
github-repo: "neex/http2smugl"
github-stars: 561
github-pushed: "2025-03-27"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# http2smugl

> Detects and probes HTTP request smuggling that becomes possible when a front end converts HTTP/2 requests to HTTP/1.1 for the back end.

## What it's for

HTTP/2 header names and values are binary and may carry characters HTTP/1.1 forbids (spaces, newlines). When a front-end server translates an HTTP/2 request into HTTP/1.1 for the back end, a smuggled `Content-Length` or `Transfer-Encoding` header that survives conversion can split one request into two on the back end — enabling protection bypass, cache poisoning and cross-user request capture. http2smugl (Go, by neex) automates the detection: it fires paired valid/invalid smuggled-header variants (appended spaces, dashes-turned-underscores, embedded newlines, Unicode case tricks) and statistically decides whether responses to the two groups are distinguishable — i.e. whether something behind the front end actually parses the smuggled header. By design it never prints "vulnerable", only that responses are separable. A companion `request` subcommand sends arbitrary raw HTTP/2 headers that normal clients refuse to emit.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Run the smuggling detection against a target

One command for the full technique battery.

```
http2smugl detect https://target.example.com/
```

Outcome: prints a report only when responses to valid vs invalid smuggled-header probes are distinguishable (non-intersecting status codes, separable lengths, or timeouts counted as their own status); silence means no signal was found.

### Send a hand-crafted HTTP/2 request

Replay a specific malformed header most tools normalize away.

```
http2smugl request https://target.example.com/ 'transfer_encoding:chunked' 'x-test:1'
```

Outcome: sends the raw HTTP/2 request with those literal headers (`name:value`, no space after the colon) — `\r`, `\n` and `\xXX` escapes are supported in names and values — and prints the server's response, letting you manually probe exactly what the conversion layer tolerates.

### Probe over HTTP/3

Check the QUIC path too.

```
http2smugl detect https+h3://target.example.com/
```

Outcome: runs the same detection over HTTP/3 (QUIC) instead of TCP HTTP/2 — experimental support, selected purely by the `https+h3://` scheme prefix.
