---
name: "shitflood"
categories:
  - blackarch-dos
version: "14.e74fc42"
homepage: "https://github.com/acidvegas/shitflood"
packaged-by: [blackarch]
blackarch-builddate: "2016-03-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "acidvegas/shitflood"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# shitflood

> Python 2 IRC clone flooder that mass-joined fake users through SOCKS5 proxies.

## What it's for

shitflood was a small Python 2 utility by acidvegas for flooding Internet Relay Chat networks with clone connections: it spun up many IRC "clones" through SOCKS5 proxies so a network or channel was overwhelmed by join/message traffic from distributed-looking sources. It was packaged by BlackArch in 2016, but the upstream repository has since been deleted and no verifiable copy survives (the author's current GitHub and self-hosted forges no longer carry it, and no archived snapshot or mirror could be located), so no source, usage, or flags can be documented. This note is description-only for that reason: no command-line interface can be verified against any surviving source, and nothing here should be treated as runnable documentation. For IRC-targeted stress testing with living upstreams, general slow HTTP tools do not apply — look at maintained IRC protocol test suites instead.

*No corpus evidence — derived from upstream documentation (upstream deleted; no verifiable CLI remains).*
