---
name: "exitmap"
categories:
  - blackarch-recon
version: "373.8155029"
homepage: "https://github.com/NullHypothesis/exitmap"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "NullHypothesis/exitmap"
github-stars: 459
github-pushed: "2023-10-16"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# exitmap

> Modular Python scanner that runs test modules over Tor exit relays, using Stem to build circuits through every exit it checks.

## What it's for

exitmap (NullHypothesis / Philipp Winter) is a fast, modular Python scanner for Tor exit relays: think of it as a `map()` interface for exit relays — a module implements a task, and exitmap runs that task over all (or a subset of) exits by creating Tor circuits through each one. Use it to measure the reliability and trustworthiness of exit relays — the Tor Project has used it to hunt false negatives in its check service and to find malicious exits (DNS poisoning, file patching, CAPTCHA injection). Built-in modules include `testfds`, `checktest`, `dnspoison`, `dnssec`, `patchingCheck`, `cloudflared` and `rtt`. It is a network-measurement research tool, not an anonymity client, and the README asks users not to run redundant scans since The Tor Project already scans regularly. The GitHub repository is unmaintained/archived; development continues in the Tor Project's fork at gitlab.torproject.org/tpo/network-health/exitmap.

## Typical use cases

### Scan all exits with a module

Run one test module over every exit relay.

```
./bin/exitmap checktest
```

Outcome: bootstraps Tor (visible in the log), builds circuits through exit relays, invokes the module for each established circuit, and prints a scan summary at the end. Replace `checktest` with any bundled module, e.g. `dnspoison` or `dnssec`.

### Pin a static first hop

Control the first hop to avoid three-hop circuits.

```
./bin/exitmap --first-hop 1234567890ABCDEF1234567890ABCDEF12345678 checktest
```

Outcome: uses the relay with the given fingerprint (one you control) as static first hop, so each scan circuit is exit-varying but entry-stable; replace the pseudo-fingerprint with your own relay.

### Restrict to exits in one country and throttle

Limit the scanned exits and pace circuit creation.

```
./bin/exitmap --country DE --build-delay 5 checktest
```

Outcome: scans only German exit relays, pausing five seconds between circuit creations to reduce load on the Tor network and the scan destination; options can also be set persistently in `~/.exitmaprc` (e.g. `first_hop`, `build_delay`, `verbosity`).

No corpus evidence — derived from upstream documentation.
