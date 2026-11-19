---
technology: "WebSocket"
kind: overview
audience: offensive-security
family: network-services
layer: full-duplex messaging protocol over HTTP upgrade (RFC 6455); socket.io/SignalR-style client libraries layered on top
language: protocol (server libraries in Python/Node/.NET; client tooling in Python and browser JS)
standards: "RFC 6455 (2011); ws:// scheme on cleartext, wss:// over TLS; subprotocols (e.g. the Chromium remote-debugging protocol) ride the same handshake"
first-released: "2011 (RFC 6455; protocol drafts from 2008)"
license: "IETF royalty-free standard"
homepage: "https://datatracker.ietf.org/doc/html/rfc6455"
provenance: "field-grounded (7 contributing writeups gated by command-context markers — ws:// URLs in client code and tool invocations, websocket client imports, socket.io traffic, Sec-WebSocket handshake lines; exclusions: chisel tunnel client logs where ws:// is only the pivot transport, Burp tab-label chrome, a Tomcat WsFilter stack trace, .NET assembly name listings) + upstream-verified (RFC 6455 handshake/origin semantics, sqlmap project changelog and merged websocket-support PR for ws:// targets). Enrichment pass 2, 2026-11-12."
---

# WebSocket

> The handshake is one HTTP GET — so every authentication scheme that forgot the Origin header, every SQL query behind a `ws.send()`, and every admin console bound to loopback is now a web endpoint that your browser tooling mostly can't see.

## What it is

WebSocket (RFC 6455, 2011) upgrades a normal HTTP request into a persistent, bidirectional socket: the client sends `GET` with `Upgrade: websocket` and a `Sec-WebSocket-Key`, the server answers `101 Switching Protocols`, and from then on both sides push frames without further HTTP semantics. It exists for chat, live dashboards, ticket validators, and collaborative editors — and it is deployed in three shapes an operator meets constantly: **raw ws servers** (Python `websockets`, Node `ws`, .NET libraries) on odd high ports, **socket.io-style libraries** that multiplex events (`user_join`, `private_recv`) over the same upgrade with long-polling fallback, and **instrumentation channels** — most notably the Chromium/CEF remote-debugging endpoint — that expose an entire application's internals over a loopback ws URL.

The security model is the story: the browser enforces same-origin on the *HTTP* upgrade, but only if the *server* validates the `Origin` header, which nothing in the protocol requires. Cookies ride the handshake automatically. So a websocket endpoint is an HTTP endpoint with all of HTTP's auth mistakes and none of HTTP's tooling defaults — no proxy history entries, no CSRF tokens, often no rate limiting — plus a second layer of failures in the message handlers that parse JSON after the connection is trusted.

## The anatomy that matters on an engagement

    browser/mobile app → HTTP GET Upgrade: websocket (+ Origin, + cookies)
                      → server accepts (Origin checked? session checked?)
                      → frames: JSON messages → handler → SQL query / file read / command
    variants:
      socket.io: GET /socket.io/?EIO=4&transport=polling ⇄ events (join, private_recv)
      CEF/Electron debug: ws://127.0.0.1:<port>/<uuid> → DevTools protocol → arbitrary JS

Three structural properties drive most findings:

- **The handshake inherits HTTP auth, then forgets it.** Session cookies authenticate the upgrade; after `101`, authorization is whatever the message handler assumes — usually "the socket is trusted."
- **Message handlers are a private API.** The JSON a client sends (`{"id": "5"}`, `{"version": "1.0"}`) lands in a query or a deserializer with no WAF, no logging, and no scanner coverage — blind SQLi and insecure deserialization hide here for years.
- **Nobody enumerates the port.** ws servers sit on non-standard ports (and on loopback) that nmap lists as `unknown`; the endpoint inventory lives in client JavaScript and compiled clients, not in the web root.

## Where you'll meet it

- **Customer-facing apps with a "live" feature** — ticket checkers, support chat, betting or status pages. The ws URL is hardcoded in page source (`new WebSocket("ws://...")`); the handler behind it does lookups straight from the message payload.
- **Internal services fronted by a public app** — an SSRF in the web tier reaches a chat or messaging service that was never meant to be internet-facing; speaking its socket protocol through the SSRF (or running its client code pointed at the internal host) is the way in.
- **Desktop and kiosk apps** — Electron/CEF applications ship a remote-debugging websocket on loopback by default in misconfigured builds; with any shell on the box, that loopback socket is a one-shot code-execution primitive with no authentication.
- **Cross-site from your own page** — wherever the server skipped the Origin check, a page you control can open the victim's websocket with the victim's cookies and quietly read everything the connection can see (cross-site websocket hijacking).

## Reading the protocol's mind

- **`Origin` is optional to enforce** — the single fact behind CSWSH; check it before anything else, because the victim's browser is your client.
- **`Sec-WebSocket-Key`/`Version: 13` in any capture means a raw handshake you can replay with a ten-line client** — no session, no tokens, unless the first message demands one.
- **socket.io degrades gracefully** — `transport=polling` requests are the same protocol over plain HTTP; if you can't hold a socket, you can still speak the events.
- **Handshake-time auth tokens come from cookies in the client code** (`getCookie(...)` passed as the `auth` payload) — steal the cookie, own the socket; and if the client can be pointed at a different auth server, the endpoint itself will follow.
- **A ws URL with a UUID path on loopback is a DevTools console** — treat it as code execution, because it is.

```js
// The client code you will find in page source — the whole recon step:
const socket = io("ws://support.example.com", { auth: { token: getCookie("SESSION") } });
socket.on("private_recv", (m) => render(m));   // <- handler trusts the socket,
socket.emit("user_join", { username: "Admin" }); // <- and joins whoever it is told
```

## Where to go next

Endpoint discovery, custom clients, blind SQLi through frames, cross-site hijacking, and loopback debugger abuse are laid out step by step in [[websocket-bug-bounty]].
