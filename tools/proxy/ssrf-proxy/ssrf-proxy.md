---
name: "ssrf-proxy"
categories:
  - blackarch-proxy
version: "293.e79da7a"
homepage: "https://github.com/bcoles/ssrf_proxy"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "bcoles/ssrf_proxy"
github-stars: 484
github-pushed: "2018-01-01"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ssrf-proxy

> Turn a Server-Side Request Forgery endpoint into a general-purpose local HTTP proxy.

## What it's for

SSRF Proxy is a multi-threaded HTTP proxy server (Ruby, by Brendan Coles) that tunnels client HTTP traffic through a server vulnerable to Server-Side Request Forgery. You give it the vulnerable URL with a placeholder marking where the client's request is inserted; it then formats each client request into the SSRF shape (URL-encoding, method, POST data, cookies), fetches it, and parses the server's response back into a clean HTTP response for the client — optionally stripping junk headers, extracting the body with a regex, and guessing status codes and MIME types. Done right, this makes the SSRF usable as a normal proxy for web browsers, proxychains, and scanning tools such as sqlmap, nmap, dirb and nikto. It can also be driven as a Ruby library (`require 'ssrf_proxy'`) for one-off SSRF requests via `send_uri`/`send_request`.

## Typical use cases

### Proxy through a GET-based SSRF

Wrap a simple URL-parameter SSRF.

```
ssrf-proxy -u "http://target.example.com/fetch?url=xxURLxx"
```

Outcome: a proxy starts on 127.0.0.1:8081; point a browser or proxychains at it and each request is URL-encoded into the `url=` parameter of the vulnerable endpoint, with the response body parsed back out as the proxy's response.

### Proxy through a POST-based SSRF

Use when the insertion point is in the request body.

```
ssrf-proxy -u "http://target.example.com/fetch" --method POST --post-data "url=xxURLxx"
```

Outcome: client requests are forwarded as POSTs whose body carries the target URL at the placeholder position, matching endpoints that read their fetch target from form data rather than the query string.

### Build the proxy from a saved raw HTTP request

Start from a captured request instead of reconstructing it in flags.

```
ssrf-proxy -f request.txt
```

Outcome: the raw HTTP request in request.txt — with the `xxURLxx` placeholder marking the insertion point (configurable via `--placeholder`) — defines the SSRF request shape; headers, cookies and auth from the capture are replayed for every proxied request.

### Clean responses so scanners work

Strip wrapper junk and repair status/MIME for tool consumption.

```
ssrf-proxy -u "http://target.example.com/fetch?url=xxURLxx" --strip "Server,X-Powered-By" --guess-status --guess-mime
```

Outcome: the headers named in `--strip` are removed from returned responses, the status code is inferred from body strings such as "404 Not Found", and the Content-Type is derived from the requested file's extension — so nmap/dirb/nikto-style tooling interprets responses correctly instead of seeing every page as a 200 from the wrapper. Add `--decode-html --unescape` when the endpoint returns entity-encoded bodies, and `--forward-cookies`/`--forward-method` when the client's own session and verb must ride along.

*No corpus evidence — derived from upstream documentation.*
