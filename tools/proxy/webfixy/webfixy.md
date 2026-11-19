---
name: "webfixy"
categories:
  - blackarch-proxy
version: "25.5d477b0"
homepage: "https://github.com/takeshixx/webfixy"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "takeshixx/webfixy"
github-stars: 14
github-pushed: "2018-10-08"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webfixy

> On-the-fly decryption proxy for MikroTik RouterOS WebFig sessions.

## What it's for

WebFig, the web management interface of MikroTik RouterOS, encrypts its HTTP traffic even without SSL/TLS, using a scheme reminiscent of PPTP (MS-CHAP-V2 authentication, MPPE key derivation, RC4 encryption) — so ordinary web proxies cannot observe or manipulate it. WebFixy breaks that barrier: it terminates the encrypted WebFig protocol on one side, speaks it natively to the RouterOS host, and presents the decrypted session to your browser on a local listener. It can also re-encrypt in the opposite direction, so requests you modify through the proxy are delivered as valid WebFig traffic (tampering support has documented limitations). Written for Python 3 with asyncio/aiohttp; the upstream repository has been archived since 2018, so it targets RouterOS versions of that era.

## Typical use cases

### Decrypt a WebFig session with default credentials

Stand up the proxy against a stock router.

```
webfixy --target 198.51.100.10
```

Outcome: the proxy logs into the router as `admin` with an empty password, starts listening on 127.0.0.1:8080, and serves the decrypted WebFig interface at http://127.0.0.1:8080 — open it in a browser and the session renders and behaves like a normal site while the proxy handles RC4 encryption and decryption in both directions.

### Target a router with real credentials and a non-standard port

Use the account and web port actually configured on the device.

```
webfixy --target 198.51.100.10 --target-port 8081 --user admin --password Password1
```

Outcome: authenticates with the supplied WebFig credentials against the router's web service on port 8081 instead of the default port 80 — required whenever the router's defaults were changed.

### Expose the listener and watch the decrypted flow

Bind elsewhere and raise the logging level.

```
webfixy --target 198.51.100.10 -H 0.0.0.0 -p 9090 -v -v
```

Outcome: the proxy binds 0.0.0.0:9090 (defaults are 127.0.0.1:8080) and repeated `-v` raises logging through info to debug, printing the decrypted request and response exchange as sessions pass through.

*No corpus evidence — derived from upstream documentation.*
