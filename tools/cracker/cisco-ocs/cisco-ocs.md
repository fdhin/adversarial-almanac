---
name: "cisco-ocs"
categories:
  - blackarch-cracker
version: "0.2"
homepage: "http://www.question-defense.com/2013/01/11/ocs-version-0-2-release-ocs-cisco-router-default-password-scanner"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-12-22"
build-system: other
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "BlackArch/cisco-ocs"
github-stars: 3
github-pushed: "2013-11-28"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cisco-ocs

> Sweeps an IP range for Cisco routers still exposing the factory-default telnet/enable credentials.

## What it's for

OCS (Offensive Cisco Scanner, v0.2 by OverIP) is a single-purpose C scanner: given an IPv4 range, it connects to port 23 on every address in order, attempts the default login `cisco`, and on a user-level prompt tries `enable` with `cisco` to reach privileged mode. Per host it reports whether the default telnet password, the default enable password, both, or neither were accepted, or that the port is filtered. There are no command-line options — the interface is exactly two arguments, and it only ever tests the `cisco`/`cisco` factory credentials, not custom wordlists.

## Typical use cases

### Sweep a subnet for default-credential routers
```
cisco-ocs 198.51.100.1 198.51.100.254
```
Outcome: walks every address in the range, telnet port 23; for each responsive host prints a verdict line such as `Default Enable Passwords found! Vulnerable Router IP: 198.51.100.10`, `Default Telnet password found.`, `Default Telnet and Enable Passwords found!`, or `Router not vulnerable.`; hosts with port 23 unreachable report `Filtered Ports`.

### Probe a single host
```
cisco-ocs 198.51.100.10 198.51.100.10
```
Outcome: runs the same default-credential check against one address (identical start and end IP), printing the same per-host verdict without sweeping.

*No corpus evidence — derived from upstream documentation.*
