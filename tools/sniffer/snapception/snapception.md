---
name: "snapception"
categories:
  - blackarch-sniffer
version: "8.c156f9e"
homepage: "https://github.com/thebradbain/snapception"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "thebradbain/snapception"
github-stars: 616
github-pushed: "2021-07-02"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snapception

> Intercepts and decrypts Snapchat images and videos received by a device routed through its MITM proxy.

## What it's for

Snapception (Brad Bain) is a proof-of-concept demonstrating that Snapchat encrypted all image and video content with a single hardcoded key: it runs a mitmdump-based interception proxy that automatically captures, decrypts, and saves every snap received by a device that is willing to route through the proxy and trust its CA certificate. It is not a passive network sniffer — the receiving device must be configured to use the host as proxy (port 8080) and must have installed the proxy's CA from mitm.it, so bystanders on the same network cannot be harvested. Decrypted snaps are written as ordinary media files to a local output directory. The Python 2 code dates from 2015 and is unmaintained; expect it to need patching against current Python and mitmproxy versions.

## Typical use cases

### Run the interception proxy

Start the proxy with full logging and wait for received snaps.

```
snapception -vv
```

Outcome: launches the mitmdump-based proxy on port 8080 and logs interception events (including mitmdump's own output with -vv); every snap received through the proxy is decrypted and saved to ~/snaps while the run continues.

### Save decrypted snaps to a specific directory

Direct output somewhere other than the default ~/snaps.

```
snapception -o /tmp/snaps
```

Outcome: decrypted snaps are written under /tmp/snaps instead of the home directory.

*No corpus evidence — derived from upstream documentation.*
