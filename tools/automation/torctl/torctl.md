---
name: "torctl"
categories:
  - blackarch-automation
version: "0.5.7"
homepage: "https://github.com/BlackArch/torctl"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2021-03-30"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "BlackArch/torctl"
github-stars: 211
github-pushed: "2024-02-25"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# torctl

> Transparently routes all system traffic — including DNS — through the Tor network, with one command.

## What it's for

torctl is a shell wrapper around tor and iptables/nftables that turns a Linux host into a Tor-transparent machine: every outbound connection and every DNS query from any application is redirected through the Tor network without per-app configuration. It also manages operational extras that matter during long anonymized sessions — switching to a new circuit (new exit IP), rotating MAC addresses on all interfaces, and wiping memory at shutdown. It does not do application-level anonymization of content (browser fingerprinting is still your problem), and it needs root because it rewrites firewall rules. Reach for it when you want whole-system Tor routing instead of a per-proxy setup.

## Typical use cases

### Route the whole system through Tor

```
sudo torctl start
```

Outcome: starts the tor service and installs firewall rules that redirect all traffic, DNS included, through the Tor network. Applications need no proxy settings; use `torctl ip` to confirm the external address is now a Tor exit node.

### Check state and current exit address

```
torctl status
torctl ip
```

Outcome: `status` prints whether the tor service and the redirect rules are up; `ip` prints the external IP as seen through Tor, letting you confirm anonymization is active before you start working.

### Rotate to a fresh circuit mid-session

```
torctl chngid
```

Outcome: signals tor to build a new circuit, so subsequent connections leave through a different exit node. Follow with `torctl ip` to verify the exit address changed.

### Stop Tor routing and return to clearnet

```
sudo torctl stop
```

Outcome: tears down the redirect rules and stops tor, restoring direct (non-Tor) connectivity for the whole system.

### Rotate MAC addresses on all interfaces

```
sudo torctl chngmac
sudo torctl rvmac
```

Outcome: `chngmac` randomizes the MAC address of every interface (useful on network re-entry); `rvmac` restores the original hardware addresses.

*No corpus evidence — derived from upstream documentation.*
