---
name: "wuzz"
categories:
  - blackarch-webapp
version: "v0.5.0.r24.g82195c6"
homepage: "https://github.com/asciimoo/wuzz"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-08-24"
build-system: go
license: AGPL3
meta-checked: "2026-11-09"
github-repo: "asciimoo/wuzz"
github-stars: 10741
github-pushed: "2026-08-04"
github-archived: false
github-issues: 42
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
---

# wuzz

> Interactive terminal tool for inspecting and hand-tuning HTTP requests, with cURL-style arguments.

## What it's for

wuzz is a terminal UI for HTTP inspection: it splits a request into editable panes (URL, query parameters, method, body, headers), sends it on Ctrl+R, and shows the response headers, body, and a searchable history of past requests. Command line arguments mirror cURL's, so a request copied from a browser's network inspector with "copy as cURL" can be replayed by replacing `curl` with `wuzz` and then edited interactively — roughly a keyboard-driven Burp Repeater in the terminal. It supports JSON and multipart form bodies, proxies, TLS version pinning, and regex or HTML/JSON-path filtering of response bodies (Ctrl+T toggles the filter syntax). It is for manual exploration of single requests, not crawling or scanning.

## Typical use cases

### Craft and send a request interactively

Open wuzz with the method, headers and body pre-filled, then edit any pane before sending.

```
wuzz -X POST -H 'Content-Type: application/json' -d '{"user":"admin"}' https://example.com/api/login
```

Outcome: the TUI opens with the request prefilled across its panes; Ctrl+R sends it and the response headers/body panes fill in; F1 shows all key bindings and F2–F9 jump between panes.

### Send a JSON body with the right headers

Use the dedicated JSON flag instead of setting Content-Type by hand.

```
wuzz -j '{"user":"admin","pass":"Password1"}' https://example.com/api/login
```

Outcome: wuzz opens with the JSON body loaded and the JSON content-type headers set, so a successful login response can be iterated on without re-typing headers.

### Replay a request copied from the browser

Paste a browser-captured request into wuzz by prefixing the copied cURL arguments with `wuzz`.

```
wuzz 'https://example.com/search?q=test' -H 'Accept: text/html' -H 'X-Requested-With: XMLHttpRequest'
```

Outcome: the same request the browser made loads into the panes; editing the query pane and pressing Ctrl+R re-issues it, and Alt+H brings up prior requests to compare responses.

### Route requests through an intercepting proxy

Chain wuzz through a local proxy such as Burp Suite or ZAP.

```
wuzz -x http://localhost:8080 https://example.com
```

Outcome: every request wuzz sends (and its response) also passes through the proxy on port 8080, so the interactive session is visible there for further testing.

### Talk to an old or strictly configured TLS server

Pin allowed TLS versions, or skip certificate verification for self-signed test endpoints.

```
wuzz -T TLS1.0,TLS1.1 -k https://target.example.com
```

Outcome: the request is negotiated only with TLS 1.0–1.1 and certificate errors are ignored, letting the TUI reach legacy lab services that a default client would refuse.

No corpus evidence — derived from upstream documentation.
