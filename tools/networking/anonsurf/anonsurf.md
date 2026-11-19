---
name: "anonsurf"
categories:
  - networking
version: "6.0.1+parrot2"
homepage: "https://gitlab.com/parrotsec/packages/anonsurf"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: distro-package
meta-checked: "2026-11-09"
github-repo: "ParrotSec/anonsurf"
github-stars: 450
github-pushed: "2026-09-25"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 156
gh-activity-checked: "2026-11-09"
---

# anonsurf

> System-wide Tor routing for Parrot OS — one command redirects the machine's TCP traffic and DNS through Tor.

## What it's for

AnonSurf (Parrot Security team) is a Go CLI (plus an optional Qt GUI) that transparently routes a host's TCP traffic and DNS through Tor using iptables and Tor's DNSPort/TransPort, managed by systemd units. Version 6 provides the commands `start`, `stop`, `restart`, `status`, `changeid`, `ip`, `monitor` and `boot enable|disable|status`, with `-k/--safe-kill` available on start/stop to handle applications that would otherwise leak outside Tor. Existing direct connections are blocked (not migrated) when routing starts, and only Tor-carried TCP plus Tor DNS is supported — it is a host-wide anonymizer, not a per-application proxy configurator. Development happens on GitLab (gitlab.com/parrotsec/packages/anonsurf); the GitHub repository is the older mirror.

## Typical use cases

### Route the whole system through Tor

```
sudo anonsurf start
```

Outcome: enables the firewall rules and Tor units; direct connections to public IPv4 hosts are dropped and TCP applications must reconnect to go through Tor. Prints `AnonSurf started.` on success.

### Verify the routing and the exit identity

```
anonsurf status && anonsurf ip
```

Outcome: `status` reports whether routing is active (add `--json` for machine-readable output, or `-n/--nyx` to attach the nyx Tor monitor); `ip` shows the public IP address as seen from the Tor exit, confirming traffic is actually anonymized.

### Rotate the exit node

```
anonsurf changeid
```

Outcome: tells Tor to build a new circuit, changing the exit identity used by subsequent connections without stopping routing.

### Enable routing at boot

```
sudo anonsurf boot enable
```

Outcome: enables the systemd unit so anonymized routing comes up automatically at boot; `boot disable` undoes it and `boot status` reports the current state. To tear everything down and restore the original network, run `sudo anonsurf stop`.

*No corpus evidence — derived from upstream documentation.*
