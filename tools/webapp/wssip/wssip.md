---
name: "wssip"
categories:
  - blackarch-webapp
version: "1.1.0.r14.g56d0d2c"
homepage: "https://github.com/nccgroup/wssip"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-19"
build-system: node
license: AGPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "nccgroup/wssip"
github-stars: 450
github-pushed: "2022-12-08"
github-archived: false
github-issues: 35
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wssip

> Man-in-the-middle proxy for WebSocket and Socket.IO traffic with a GUI for capturing, editing and replaying frames.

## What it's for

WSSiP ("WebSocket/Socket.io Proxy") is an Electron application that acts as an HTTP(S) proxy and exposes every WebSocket and Socket.IO frame passing between client and server for inspection, modification, interception and replay — the WebSocket counterpart to what Burp Suite does for HTTP. Point a browser at its listener (localhost:8080 by default), open any WebSocket-using page, and each connection appears with its full message history, editable before forwarding. It can chain into an upstream intercepting proxy (Tools > Use Upstream Proxy) so HTTP traffic goes to Burp or Pappy while WebSocket traffic stays in WSSiP, and it exposes an HTTP "fuzzing bridge" so external scripts in any language can query and inject frames on live connections. It is an interactive GUI tool, not a batch scanner.

## Typical use cases

### Capture and inspect WebSocket traffic

Start the proxy and route a browser through it while using a WebSocket application.

```
wssip
```

Outcome: the application opens and listens on localhost:8080; after configuring the browser's HTTP proxy to http://localhost:8080/ and visiting a WebSocket page, each connection and every client/server frame appears in the UI, where messages can be modified, dropped, or replayed by hand.

### Query a live WebSocket connection from a script

Ask the fuzzing bridge whether captured connection #1 is still open.

```
curl -x http://localhost:8080 http://mitm/ws/1
```

Outcome: a JSON status document from the bridge, e.g. `{"connected":true,"url":"ws://target.example.com","bytesReceived":0,"readyState":3}`, usable to let an external fuzzer pick an active connection id. The `mitm` hostname is served by WSSiP itself and only resolves through the proxy.

### Inject a frame into a live WebSocket

Push data into a captured connection as if it came from the client.

```
curl -x http://localhost:8080 -X POST --data 'hello' 'http://mitm/ws/1/client/message/ascii'
```

Outcome: the bridge returns `{"success":true}` and the frame is delivered to the server on that WebSocket; `client` can be swapped for `server`, and `message` for `ping`/`pong`, to test how each side handles unexpected frames.

### Export the interception CA certificate

Fetch the proxy's root certificate for trusting it in a browser or other client.

```
curl -x http://localhost:8080 http://mitm/ca.pem -o wssip-ca.pem
```

Outcome: writes `wssip-ca.pem`; importing it as a trusted CA lets WSSiP decrypt HTTPS-upgraded WebSocket (wss) traffic instead of failing certificate validation.

No corpus evidence — derived from upstream documentation.
