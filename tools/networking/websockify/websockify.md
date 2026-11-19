---
name: "websockify"
categories:
  - blackarch-networking
version: "998.1e284ea"
homepage: "https://github.com/novnc/websockify"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-25"
build-system: python
license: LGPL3
meta-checked: "2026-11-09"
github-repo: "novnc/websockify"
github-stars: 4491
github-pushed: "2026-10-21"
github-archived: false
github-issues: 48
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---

# websockify

> WebSocket-to-TCP bridge from the noVNC project: lets browser clients talk to any plain TCP service.

## What it's for

websockify accepts WebSocket (HyBi / IETF 6455) connections from a browser and transparently relays the traffic to an ordinary TCP server, and back. It was originally written by Joel Martin (kanaka) as part of the noVNC project and now lives under the noVNC GitHub organization; its canonical use is putting VNC behind noVNC's HTML5 client, but it works for any TCP protocol. Beyond plain bridging it can serve static web content (`--web`), do TLS/wss:// (`--cert`, `--key`, `--ssl-only`), record sessions (`--record`), route clients to multiple targets via token or hostname (`--token-plugin`, `--host-token`), demand authentication (`--auth-plugin`), daemonize (`-D`), and even wrap a local program instead of proxying to a remote host. It is purely a transport bridge — it does not itself speak VNC or any other application protocol.

## Typical use cases

### Bridge a browser to a remote TCP service

Expose a service that speaks plain TCP to WebSocket clients.

```
websockify 6080 198.51.100.10:5900
```

Outcome: Listens on local port 6080 and forwards WebSocket connections bidirectionally to the VNC server at 198.51.100.10:5900; a noVNC (or any WebSocket) client pointed at the proxy reaches that service.

### Serve the web client and the WebSocket proxy on one port

Host the HTML client and its WebSocket connection from the same URL.

```
websockify --web /usr/share/webapps/novnc 6080 localhost:5900
```

Outcome: Port 6080 answers both ordinary HTTP requests — serving files from the given directory — and WebSocket upgrades, so a single URL hosts the client app and its proxied connection to localhost:5900.

### Encrypt the bridge with TLS (wss://)

Make browser-to-proxy traffic encrypted.

```
websockify --cert self.pem --ssl-only 8443 localhost:5900
```

Outcome: websockify sniffs the first client byte, detects TLS, and serves wss:// on port 8443, refusing plain connections because of `--ssl-only`. A combined certificate/key file for `--cert` can be generated with `openssl req -new -x509 -days 365 -nodes -out self.pem -keyout self.pem`.

### Launch a local program and proxy into it

Start a service that binds its own port and bridge clients to it.

```
websockify 5901 --wrap-mode=ignore -- vncserver -geometry 1024x768 :1
```

Outcome: websockify launches the command after `--`, uses its LD_PRELOAD `rebind.so` trick to move the program's bound port to a free loopback port, and proxies WebSocket clients on 5901 to it; `--wrap-mode=ignore` leaves the program running when it daemonizes (use `--wrap-mode=respawn` for programs that exit per connection).

*No corpus evidence — derived from upstream documentation.*
