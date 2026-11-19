---
name: "arpon"
categories:
  - blackarch-defensive
version: "3.1"
homepage: "https://arpon.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-03"
build-system: cmake
license: custom
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/arpon"
github-stars: 0
github-pushed: "2025-12-03"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# arpon

> ARP handler inspection daemon that blocks ARP-spoofing MITM attacks by policing the host's ARP cache.

## What it's for

ArpON is a host-side daemon that takes over ARP-cache management on a chosen interface so spoofed ARP replies — the basis of ARP-spoofing man-in-the-middle, cache-poisoning, and poison-routing attacks — cannot install attacker-controlled entries. Three inspection modes cover different networks: SARPI for statically addressed networks without DHCP (entries pinned in the config file), DARPI for DHCP networks (dynamic entries accepted only under a strict request/reply policy), and HARPI combining both. While running it pins the kernel `arp_ignore`/`arp_accept` parameters on the interface and restores them on clean shutdown; `SIGHUP` reloads the configuration. It stops ARP-layer MITM only — it does not encrypt traffic and does not defend other protocol layers.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Protect a DHCP-managed interface (DARPI)

Guard a laptop or workstation on a dynamic network.

```
sudo arpon -i eth0 -D
```

Outcome: the daemon runs in the foreground managing all dynamic ARP entries on `eth0`; gratuitous or poisoned replies that do not satisfy the inspection policy are denied and cleaned from the cache, so a spoofer's MAC never becomes the gateway's entry. Interrupt with Ctrl+C to exit and restore the kernel ARP parameters.

### Protect a static network (SARPI)

Enforce pinned address mappings on fixed-addressing segments.

```
sudo arpon -i eth0 -S
```

Outcome: only the static IP/MAC entries declared for the interface in the ArpON configuration file are permitted to sit in the cache; entries not matching the policy are refused — the right mode for servers on static addressing without DHCP.

### Handle mixed static and dynamic hosts (HARPI)

Apply both policies where a segment carries both kinds of hosts.

```
sudo arpon -i eth0 -H
```

Outcome: hybrid inspection pins the static entries from the config while policing dynamic entries with the DARPI handshake policy — the general-purpose choice when a network mixes servers on static IPs with DHCP clients.

### Run it as a background daemon

Keep the protection running after logout.

```
sudo arpon -d -i eth0 -D
```

Outcome: `-d` daemonizes the same protection (pair it with an init/service unit for boot persistence); send `SIGHUP` to reload the configuration and `SIGTERM` for a clean stop that restores kernel ARP parameters. Avoid `SIGKILL` — it skips the restore and leaves `arp_ignore`/`arp_accept` pinned.
