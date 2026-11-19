---
name: "http-traceroute"
categories:
  - blackarch-networking
version: "0.5"
homepage: "http://packetstormsecurity.com/files/107167/Traceroute-Like-HTTP-Scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# http-traceroute

> Traceroute for the HTTP layer: enumerates the reverse proxies in front of a service by stepping the Max-Forwards header and diffing each hop's response.

## What it's for

http-traceroute (v0.5, Nicolas Grégoire, building on Julien Cayssol's earlier versions) is a Python 2 script that maps the chain of proxies in front of a web service. It issues repeated requests with increasing Max-Forwards values (RFC 2616 section 14.31; SIP carries the same header per RFC 3261 section 8.1.1.6) — each proxy that decrements the header to zero returns an error, revealing one hop per request — then compares consecutive hops' status code, Server, Content-Type, Via, X-Forwarded-For and HTML title/address and scores the differences to flag where a reverse proxy, load balancer, or WAF sits. The default method is TRACE against `/` over plain HTTP. It maps the application-layer chain only — it is not an IP-layer traceroute.

## Typical use cases

### Trace the proxy chain of a host

```
http-traceroute -t example.com
```

Outcome: sends TRACE requests with growing Max-Forwards values and prints each hop's status line and distinguishing headers, ending with a heuristic score — differences such as 502/483 status codes, Via/X-Via headers, or changing Server strings indicate intermediary devices worth investigating.

### Trace an HTTPS endpoint with GET

```
http-traceroute -t example.com -s https -p 443 -m GET -v 1
```

Outcome: the same hop-by-hop walk over TLS using GET (for servers that reject TRACE), with verbose output flagging each heuristic as it fires at every hop.

### Probe a specific endpoint deeper

```
http-traceroute -t example.com -P /api/health -f 5 -v 2
```

Outcome: traces the chosen path with a Max-Forwards ceiling of 5 at debug verbosity — the fullest per-hop detail, useful when a deep proxy chain truncates before reaching the origin.

*No corpus evidence — derived from upstream documentation.*
