---
name: "mitm"
categories:
  - blackarch-networking
version: "8.bd2b351"
homepage: "https://github.com/Th3Hurrican3/mitm"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Th3Hurrican3/mitm"
github-stars: 46
github-pushed: "2021-02-09"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mitm

> Interactive Python script that pairs ARP poisoning of a chosen victim with DNS spoofing on the local network.

## What it's for

`mitm` (originally by blackeko; the packaged BlackArch sources clone Th3Hurrican3's copy of the same script) is a single-purpose, menu-driven LAN attack: it enables IP forwarding, ARP-poisons the traffic between one victim host and the gateway in both directions, and then answers the victim's DNS queries itself, so named services resolve to addresses the attacker controls. Everything is guided — there are no command-line flags; you pick the interface, let it scan the subnet, and select the victim from the list. It depends on scapy and needs root. It does not terminate TLS: only traffic whose DNS it can steer to plain-HTTP or attacker-hosted services is affected.

## Typical use cases

### Poison one LAN victim and spoof its DNS

```
sudo mitm
```

Outcome: after a root check the script enables IP forwarding, prints the available interfaces for you to choose, scans the subnet for live hosts, and asks which one to attack (showing attacker/gateway/victim IPs before starting). Once running it ARP-spoofs victim↔gateway and intercepts the victim's DNS requests; Ctrl-C tears the poisoning down.

*No corpus evidence — derived from upstream documentation.*
