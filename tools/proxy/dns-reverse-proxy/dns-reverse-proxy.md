---
name: "dns-reverse-proxy"
categories:
  - blackarch-proxy
version: "45.e3d33b9"
homepage: "https://github.com/StalkR/dns-reverse-proxy"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-05"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "StalkR/dns-reverse-proxy"
github-stars: 192
github-pushed: "2026-07-03"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# dns-reverse-proxy

> Split DNS routing by domain: a reverse proxy that forwards each query to the right upstream resolver.

## What it's for

dns-reverse-proxy (Go) is to DNS what an HTTP reverse proxy is to web traffic. It listens on TCP and UDP, IPv4 and IPv6, and forwards each incoming query to an upstream DNS server chosen by domain — a per-suffix route table with an optional default resolver for everything unmatched. Use it to serve internal zones from an internal resolver while passing the rest to a public one, to front resolvers you do not want clients addressing directly, or to consolidate several resolvers behind a single endpoint. Because upstreams see only the proxy's address rather than real client IPs, zone transfers (AXFR/IXFR) are governed by an explicit allow list. It only forwards queries — no answer rewriting, filtering or spoofing.

## Typical use cases

### Route one domain tree to its own resolver

```
dns-reverse-proxy -address :53 -default 8.8.8.8:53 -route .example.com.=8.8.4.4:53
```

Outcome: queries for names under example.com are sent to 8.8.4.4, and every other name to the 8.8.8.8 default. Omit `-default` and unmatched names are answered with a failure instead — a fail-closed route table.

### Permit zone transfers only from chosen hosts

```
dns-reverse-proxy -address :53 -default 8.8.8.8:53 -allow-transfer 198.51.100.10,2001:db8::1
```

Outcome: AXFR/IXFR requests are honored only when they arrive from the listed client IPs (comma-separated, IPv4 and IPv6 mixed); transfer attempts from any other source are not forwarded to the upstream.

*No corpus evidence — derived from upstream documentation.*
