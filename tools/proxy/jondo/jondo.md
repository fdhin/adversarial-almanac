---
name: "jondo"
categories:
  - blackarch-proxy
version: "00.20.001"
homepage: "https://anonymous-proxy-servers.net/"
packaged-by: [blackarch]
blackarch-builddate: "2020-09-08"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "jondos/JonDonym"
github-stars: 0
github-pushed: "2021-11-08"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jondo

> Java client for the JonDonym mix-cascade network (the successor of the JAP anonymity service): routes application traffic through a chain of independently operated mixes so no single operator sees both source and destination.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Anonymity for browsing and other client traffic: instead of one proxy you trust, JonDo builds layered connections through a mix cascade — typically a first mix that receives user requests, a middle mix that only forwards, and an exit mix that makes the real request — each run by independent operators, with the client selecting a cascade and presenting a local proxy to applications. The packaged 00.20.001 release is the JAP-era lineage: it ships `JAP.jar` under `/usr/share/java/jondo/` and keeps per-user settings in `~/.jap.conf`. What it does not do: it is not a high-throughput SOCKS endpoint to push scans through — free cascades are shared and slow, and the client is designed for interactive application traffic. The service and client are legacy (2020-era packaging; the client dates back further); confirm the service is still operating before depending on it.

## Typical use cases

### Launch the anonymity client

```
jondo
```

Outcome: the launcher validates the installed JVM (refusing GNU gcj) and starts the Java client window with update checks disabled; from its GUI you select a mix cascade, switch the anonymisation on, and configure your applications to send their traffic through the client instead of connecting directly — settings persist in `~/.jap.conf` between runs.
