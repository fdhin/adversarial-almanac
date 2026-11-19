---
name: "honeypy"
categories:
  - blackarch-honeypot
version: "600.6ca3d6e"
homepage: "https://github.com/foospidy/HoneyPy"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "foospidy/HoneyPy"
github-stars: 471
github-pushed: "2024-03-21"
github-archived: true
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# honeypy

> Plugin-based Python honeypot that runs many emulated TCP/UDP services at once and routes every captured session to pluggable loggers.

## What it's for

HoneyPy (foospidy) is a low-interaction honeypot designed to be raised toward medium interaction by plugins: each emulated service — examples include Elasticsearch and SIP — is a plugin whose behaviour determines how much an attacker can actually do, and each output — local log file, Twitter, the HoneyDB web endpoint — is likewise a logger plugin, so one sensor can collect locally and forward centrally at the same time. Ports and plugin assignments live in `etc/services.cfg` with behaviour profiles under `etc/profiles`, letting a single host present a broad deceptive surface that is reconfigured without code changes. The project is no longer in active development (the author recommends honeydb-agent for new deployments) and it runs on end-of-life Python 2, so treat it as a compact reference design for plugin-driven honeypots and a feed source rather than a maintained sensor.

## Typical use cases

### Start the multi-service decoy

Launch all emulated services defined in the configuration.

```
honeypy
```

Outcome: HoneyPy reads `etc/services.cfg` and binds every configured TCP/UDP plugin listener; each connection and its payload are logged through the configured loggers (by default the local log file), giving one host that answers on many service ports at once.

*No corpus evidence — derived from upstream documentation.*
