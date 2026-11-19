---
name: "netcon"
categories:
  - blackarch-networking
version: "0.1"
homepage: "https://github.com/BlackArch/netcon"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-04"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "BlackArch/netcon"
github-stars: 3
github-pushed: "2014-02-20"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netcon

> Brings a network interface up under a chosen profile — randomizing (or preserving) the MAC and hostname identifiers — and tears it back down cleanly.

## What it's for

netcon (netcon.sh, by noptrix of NullSecurity, packaged by BlackArch) establishes and manages network connections while encoding an operational-security choice in a flag. "Public" connection profiles randomize the MAC address and other client identifiers before obtaining a lease; other profiles connect with the machine's real identity. The script wraps interface bring-up and DHCP, can install a static ARP entry, can append WPA networks to the system's wpa_supplicant.conf, and has a cleanup mode that disconnects, stops what it started and reverts its changes. It needs root, manages connections only — it does not scan or attack anything — and its built-in `?` arguments enumerate every profile it knows.

## Typical use cases

### Connect on an interface with the default hardening

```
sudo netcon -i eth0
```

Outcome: brings eth0 up under the default network and connection types — MAC randomized, static ARP entry set, lease acquired; add `-v` to watch each step on stdout.

### Enumerate the available profiles before choosing one

```
netcon -n ?
netcon -c ?
```

Outcome: `-n ?` prints the selectable network types and `-c ?` the connection types with their meanings, so you can pick numeric values to pass; `-o ?` additionally shows per-type options, their defaults, and example usage.

### Connect on a trusted network without touching the MAC

```
sudo netcon -i eth0 -m
```

Outcome: same bring-up as the default run but keeps the hardware MAC address (`-m`); adding `-a` also skips the static ARP entry — the honest-identity profile for networks you own.

### Tear everything down again

```
sudo netcon -i eth0 -d
```

Outcome: disconnects the interface, stops the services the script started for the connection, and cleans up the state it created.

*No corpus evidence — derived from upstream documentation.*
