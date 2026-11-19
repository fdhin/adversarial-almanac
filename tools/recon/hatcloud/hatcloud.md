---
name: "hatcloud"
categories:
  - blackarch-recon
version: "33.3012ad6"
homepage: "https://github.com/HatBashBR/HatCloud"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "HatBashBR/HatCloud"
github-stars: 525
github-pushed: "2023-08-17"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hatcloud

> Ruby one-liner that lifts a Cloudflare-protected domain's real IP.

## What it's for

hatcloud takes a site sitting behind Cloudflare's reverse proxy and tries to
discover the origin server's real IP address, printing it when found. Use it
when assessing your own infrastructure's exposure — if the origin IP is
discoverable, direct-to-origin attacks (and DDoS traffic) bypass Cloudflare
entirely, which is exactly the gap this tool highlights. It is a single Ruby
script with essentially one switch (`-b`/`--byp` plus a domain); the project
is marked discontinued upstream, so the lookup sources it relied on may be
stale — confirm any result independently (for example by checking whether
the claimed origin actually serves the target's certificate or content)
before acting on it. It does not scan or exploit anything; it is purely an
IP-lookup helper.

## Typical use cases

### Bypass Cloudflare for one site

```
hatcloud -b target.example.com
```

Outcome: prints the discovered backend IP for the domain if one is found in
the sources the script checks; verify the origin actually serves the target
(e.g. `curl --resolve target.example.com:443:<ip>`) before treating it as
confirmed.

*No corpus evidence — derived from upstream documentation.*
