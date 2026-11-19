---
name: "okadminfinder"
categories:
  - blackarch-webapp
version: "122.a165681"
homepage: "https://github.com/mIcHyAmRaNe/okadminfinder3"
packaged-by: [blackarch]
blackarch-builddate: "2025-02-01"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "mIcHyAmRaNe/okadminfinder3"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# okadminfinder

> Locates admin panels and login pages behind a website using a large built-in list of candidate paths.

## What it's for

OKadminFinder probes a target site for administrative interfaces — more than 1000 potential admin-panel paths — and reports the ones that respond. It rotates user agents and can route its traffic through HTTP/HTTPS proxies, SOCKS4/5, or Tor, making scans harder to attribute and filter. Written in Python 3; the original GitHub repository has been deleted and the PyPI package remains the distribution channel.

## Typical use cases

### Find the admin panel of a site

```
okadminfinder -u https://example.com
```

Outcome: requests each candidate admin path under the target with rotating user agents and prints the admin panels and login pages found, with progress shown as it works through the list.

### Scan through a proxy

```
okadminfinder -u https://example.com --proxy 127.0.0.1:8080
```

Outcome: all discovery requests are sent through the given proxy, letting an intercepting proxy capture them or a relay hide the origin; with a local Tor daemon running, SOCKS support serves the same purpose anonymously.

No corpus evidence — derived from upstream documentation (repo deleted; archived README).
