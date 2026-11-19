---
name: "proxycheck"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://packetstormsecurity.com/files/61864/proxycheck.pl.txt.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# proxycheck

> proxycheck.pl — sends one HTTP CONNECT through a web server or proxy and prints the raw response, to confirm or refute open-proxy findings.

## What it's for

A single-purpose Perl script (2007, Tom Van de Wiele): it connects to a host and port, issues `CONNECT rhost:rport HTTP/1.0` with a chosen User-Agent, and prints the verbose response. It exists to verify open-proxy findings from automated web scanners — automated tools often report "proxy" on any 200 response, and this shows exactly what the server said to a real CONNECT request. Nothing more: one request, one response, no scanning.

## Typical use cases

### Test whether a server proxies CONNECT requests

Manually check a suspected open proxy.

```
perl proxycheck.pl target.example.com 8080
```

Outcome: opens TCP to target.example.com:8080, sends `CONNECT www.google.com:80 HTTP/1.0` (the built-in defaults when no redirect target is supplied), and prints the server's full response — a 200 with an established tunnel confirms proxying; a 405/501 or HTML error page refutes the scanner's finding.

### Verify against a specific target and user agent

Steer the CONNECT destination and control the client fingerprint.

```
perl proxycheck.pl target.example.com 3128 example.com 443 "Mozilla/5.0"
```

Outcome: the same single request, but aimed at example.com:443 with the given User-Agent — the form used when the probe must look like a normal browser or when testing which back-end hosts the proxy will reach.

*No corpus evidence — derived from upstream source (proxycheck.pl from PacketStorm; positional argument handling read directly in the script).*
