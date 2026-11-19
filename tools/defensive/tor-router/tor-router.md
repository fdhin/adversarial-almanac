---
name: "tor-router"
categories:
  - blackarch-defensive
version: "22.5b79c1c"
homepage: "https://github.com/Edu4rdSHL/tor-router"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-01-20"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Edu4rdSHL/tor-router"
github-stars: 311
github-pushed: "2024-01-18"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tor-router

> Route a whole Linux host's IPv4 traffic — DNS included — through Tor as a transparent proxy.

## What it's for

tor-router is a root-run shell script (plus a systemd unit) that turns a local Tor daemon into a host-wide transparent proxy: it installs iptables NAT rules so every outbound IPv4 connection and every DNS query is redirected into Tor's `TransPort`/`DNSPort`. Unlike older transparent-proxy setups it does not move or overwrite system files — the routing lives purely in netfilter rules, so stopping the script restores normal connectivity. Limits inherited from Tor: no UDP at all, and IPv4 only. Tor itself must be configured with the transparent-proxy ports before the script is started.

## Typical use cases

### Enable transparent Tor routing for the session

```
printf 'VirtualAddrNetwork 10.192.0.0/10\nAutomapHostsOnResolve 1\nTransPort 9040\nDNSPort 5353\n' | sudo tee -a /etc/tor/torrc
sudo systemctl restart tor
sudo tor-router start
```

Outcome: tor-router installs the NAT rules that send all IPv4 traffic and DNS through the local Tor daemon; confirm at `https://check.torproject.org` (should report you are routed through Tor) and with an extended test at `https://dnsleaktest.com` (should show only Tor exits, no local resolver).

### Apply the routing at every boot

```
sudo systemctl enable --now tor-router.service
```

Outcome: the packaged unit (`ExecStart=/usr/bin/tor-router start`, `Requires=tor.service`) reapplies the transparent-proxy rules automatically after each boot.

### Stop routing and restore plain connectivity

```
sudo tor-router stop
```

Outcome: the script removes its iptables rules and traffic immediately exits via the normal default route; the systemd unit's `ExecStop` invokes this same subcommand.

*No corpus evidence — derived from upstream documentation.*
