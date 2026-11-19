---
name: "h2csmuggler"
categories:
  - blackarch-webapp
version: "7.7ea573a"
homepage: "https://github.com/BishopFox/h2csmuggler"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "BishopFox/h2csmuggler"
github-stars: 821
github-pushed: "2022-05-10"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# h2csmuggler

> Detects and exploits h2c (HTTP/2 cleartext) upgrade smuggling to tunnel requests past a reverse proxy's access controls.

## What it's for

When a front-end proxy forwards client-supplied `Upgrade`/`Connection` headers to an h2c-capable back end (insecure `proxy_pass` configurations), a client can upgrade the connection to HTTP/2 cleartext and then speak HTTP/2 directly to the back end — bypassing the proxy's routing and deny rules entirely. h2csmuggler (BishopFox; the packaged `h2csmuggler` command wraps h2csmuggler.py) automates both halves: finding endpoints that forward the upgrade, and smuggling arbitrary requests — custom verbs, headers and bodies — through the resulting tunnel. HTTPS services that accept the upgrade are usually true positives; cleartext front ends may upgrade themselves without forwarding, so treat those as candidates only. The fix lives in the proxy configuration (never forward user-supplied Upgrade/Connection values), not in the back end.

## Typical use cases

### Test one endpoint for h2c upgrade forwarding

Confirm whether a proxy passes upgrade headers to an h2c back end before investing in exploitation.

```
h2csmuggler -x http://target.example.com/api/ --test
```

Outcome: performs a single upgrade probe against the endpoint behind `-x` and reports whether an h2c tunnel can be established (`101 Switching Protocols` alone is not proof — the tool verifies the tunnel carries data).

### Request a blocked resource through the tunnel

Reach endpoints the proxy denies by smuggling the request over the upgraded connection.

```
h2csmuggler -x http://target.example.com http://target.example.com/admin/
```

Outcome: upgrades via the proxy, then issues the smuggled request for the target URL (the positional argument needs a scheme) over HTTP/2 and prints the back end's response — access controls enforced only at the proxy do not apply.

### Smuggle a crafted request with custom verb, headers and body

Push an authenticated-style or content-tampered request past the edge.

```
h2csmuggler -x https://target.example.com -X POST -d '{"user": 128457, "role": "admin"}' -H "Content-Type: application/json" -H "X-SYSTEM-USER: true" http://backend.example.com/api/internal/user/permissions
```

Outcome: the POST with the injected headers and body is sent through the tunnel to the internal endpoint and its response printed; the same shape works for `X-Forwarded-For`/`X-Real-IP` spoofing to reach internal dashboards.

### Scan a list of endpoints for forwardable upgrades

Sweep discovered directories for vulnerable proxy_pass rules.

```
h2csmuggler --scan-list urls.txt --threads 5
```

Outcome: tests every URL in `urls.txt` (one full URL per line) for h2c upgrade forwarding with 5 threads; vulnerable endpoints print to stdout, while handshake/timeout errors go to stderr — redirect `1>results.txt 2>errors.txt` to capture each stream separately.
