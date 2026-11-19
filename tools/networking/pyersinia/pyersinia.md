---
name: "pyersinia"
categories:
  - blackarch-networking
version: "49.73f4056"
homepage: "https://github.com/nottinghamprisateam/pyersinia"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "nottinghamprisateam/pyersinia"
github-stars: 61
github-pushed: "2020-04-20"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pyersinia

> Yersinia-style layer-2 and DHCP network attack tool rewritten in Python on top of Scapy.

## What it's for

Pyersinia (Nottingham Prisa Team) re-implements the classic Yersinia attack surface as an extensible Python tool: ARP poisoning, rogue DHCP serving, DHCP discover floods, and Spanning Tree abuse (root-role takeover, BPDU confusion, topology-change floods). Attacks are selected with a single `-a` flag and run over a chosen interface, which makes it convenient for exercising the control-plane behavior of lab switches and routers, e.g. emulated in GNS3. New attacks are added as plugins through the STB (Security Tool Builder) framework. It is an attack/awareness tool for authorized lab testing, not a hardening or auditing suite.

## Typical use cases

### List the available attacks

Check which attack types and global options the installed build supports before running anything.

```
pyersinia -h
```

Outcome: prints the banner, the supported attack types (arp_poison, dhcp_rogue, dhcp_discover_dos, stp_root_role, stp_bdpu_conf, stp_tcn) and the global options — `-i` interface, `-g` gateway IP, `-s` DHCP server IP, `-n` network, `-m` netmask, `--domain`/`--sdomain` for DHCP options.

### Poison ARP between two hosts

Disrupt or intercept traffic between a target and its peer on the local segment.

```
pyersinia -a arp_spoof 198.51.100.10 198.51.100.20
```

Outcome: starts ARP spoof/poison traffic for the TARGET and VICTIM addresses given as positional arguments; run it from a host on the same segment with IP forwarding enabled to place it between the two.

### Take over the Spanning Tree root role

Force the attacking host to be elected STP root on a switched segment.

```
pyersinia -a stp_root -i eth0
```

Outcome: emits forged superior BPDUs on eth0 so participating switches reconverge around the attacking machine; the related attack types stp_bdpu_conf and stp_tcn disturb the same protocol differently.

### Stand up a rogue DHCP server

Answer DHCP discovers on a segment with attacker-controlled lease parameters.

```
pyersinia -a dhcp_rogue -i eth0 -g 198.51.100.1 -s 198.51.100.10 -n 198.51.100.0 -m 255.255.255.0
```

Outcome: offers leases handing clients the specified gateway (`-g`), server address (`-s`), network (`-n`) and netmask (`-m`); `-a dhcp_discover_dos` is the sibling attack that exhausts the real server with discovers.

*No corpus evidence — derived from upstream documentation.*
