---
name: "hyperfox"
categories:
  - blackarch-networking
version: "121.1a8c26f"
homepage: "https://github.com/malfunkt/hyperfox"
packaged-by: [blackarch, aur]
blackarch-builddate: "2021-04-26"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "malfunkt/hyperfox"
github-stars: 1627
github-pushed: "2023-10-17"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hyperfox

> José Nieto's (xiam) HTTP/HTTPS interception recorder — a transparent Go proxy that stores every request and response pair in SQLite and plays them back through a built-in web UI.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Recording and reviewing web traffic between a client and the network. Hyperfox runs a transparent HTTP proxy (default 127.0.0.1:1080), automatically creates a numbered SQLite database per run (`hyperfox-00123.db`) holding every request/response exchange, and can serve a web UI (default 127.0.0.1:1984) plus a minimal REST API for browsing the captures. HTTPS can be intercepted when you supply a root CA via `-ca-cert`/`-ca-key`: Hyperfox then answers TLS connections on the HTTPS port with on-the-fly certificates minted from the connection's SNI, and records the decrypted exchanges like plain HTTP. It only sees traffic routed through it — point clients at it explicitly (proxy setting, `/etc/hosts`, DNS) or pair it with an ARP-spoofing helper such as the author's arpfox for LAN-wide capture. The project lived at github.com/xiam/hyperfox and the repository has since moved to github.com/malfunkt/hyperfox.

## Typical use cases

### Run the proxy and push traffic through it

```
hyperfox &
curl -x http://127.0.0.1:1080 http://example.com
```

Outcome: the proxy listens on 127.0.0.1:1080, reads the plaintext request, uses its Host header to find the destination, and forwards it; the console logs a line like `127.0.0.1:44254 - - [11/Apr/2020:19:19:48 -0500] "GET http://example.com/ HTTP/1.1" 200 -1`, and the full exchange is written to a freshly created `hyperfox-00123.db` SQLite database in the working directory.

### Record with the web UI

```
hyperfox -db records.db -ui
```

Outcome: traffic goes into records.db and the dashboard opens at 127.0.0.1:1984 (with a small REST API on 127.0.0.1:4891 guarded by a per-run random key); add `-ui-addr 198.51.100.10:1984` to bind elsewhere — when the UI address differs from 127.0.0.1, Hyperfox prints a QR code so mobile devices can reach it.

### Intercept HTTPS with a custom root CA

```
sudo hyperfox -ca-cert ./rootCA.crt -ca-key ./rootCA.key -https 443
```

Outcome: opens a TLS interception listener on port 443; a client that trusts the root CA (e.g. `curl -k --resolve example.com:443:127.0.0.1 https://example.com`) gets an SNI-matched certificate signed by that CA, and the decrypted session is recorded — the log line now reads `"GET https://example.com/ HTTP/2.0" 200 -1`.

### Capture transparently via /etc/hosts

```
sudo hyperfox -ui -http 80 -dns 8.8.8.8
```

Outcome: with `example.com 127.0.0.1` added to /etc/hosts, Hyperfox binds HTTP port 80, resolves upstream names itself through the given resolver (bypassing the OS resolver that now loops back), transparently proxies requests to the real example.com, and shows them live in the UI.
