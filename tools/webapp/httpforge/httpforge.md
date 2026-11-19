---
name: "httpforge"
categories:
  - blackarch-webapp
version: "11.02.01"
homepage: "http://packetstormsecurity.com/files/98109/HTTPForge.02.01.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# httpforge

> Three unix-style filters — httpforge, httpsend, httpextract — to build, send and dissect raw HTTP messages from the command line.

## What it's for

HTTPForge (by Antoine d'Otreppe, distributed via PacketStorm) manipulates HTTP at the wire level: `httpforge` renders or edits a raw HTTP request (method, URI, protocol version, headers, body, computed Content-Length), `httpsend` takes a raw request on stdin, delivers it over a plain TCP socket and prints the response, and `httpextract` pulls one chosen part (status line, headers, a named header, body, protocol) out of a raw response on stdin. Piped together they give byte-exact control over what is sent — no client library normalizing headers or bodies — for testing, discovering and asserting the behavior of web servers and applications. It speaks cleartext HTTP only (no TLS), and it is Python 2 era code: modern interpreters cannot run it unmodified.

*No corpus evidence — derived from the packaged upstream source (PacketStorm tarball).*

## Typical use cases

### Forge a raw request

Build the exact bytes before sending anything.

```
httpforge -m POST -u /api/login -s "Host: target.example.com" -s "Content-Type: application/json" -b '{"user":"user","pass":"Password1"}' -l
```

Outcome: prints the complete raw HTTP request to stdout — request line, headers (with Content-Length computed by `-l`) and body — ready to inspect or pipe onward.

### Send a forged request to a server

Deliver the crafted bytes and see the raw answer.

```
httpforge -m GET -u / -s "Host: target.example.com" | httpsend -p 80
```

Outcome: the request is written to a raw TCP socket to the Host header's destination on port 80 (`-a` overrides the address) and the full raw response is written to stdout.

### Extract one part of a response

Turn a raw response into exactly the field you want.

```
httpforge -m GET -u / -s "Host: target.example.com" | httpsend | httpextract header Server
```

Outcome: parses the response from stdin and prints only the value of the Server header (`body`, `status`, `head` and `protocol` are the other selectable parts) — a one-line banner check.

### Rewrite a captured request

Alter an existing raw request without rebuilding it.

```
cat request.txt | httpforge -a -u /admin -s "X-Forwarded-For: 127.0.0.1"
```

Outcome: reads the request from stdin, replaces its URI with `/admin`, adds the header, and prints the modified raw request for re-sending through `httpsend`.
