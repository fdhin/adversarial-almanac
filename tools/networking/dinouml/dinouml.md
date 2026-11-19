---
name: "dinouml"
categories:
  - blackarch-networking
version: "0.9.5"
homepage: "http://kernel.embedromix.ro/us/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/DinoUML"
github-stars: 0
github-pushed: "2019-09-08"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dinouml

> Simulates large Linux networks on a single machine by orchestrating User Mode Linux guests.

## What it's for

DinoUML is a wrapper around User Mode Linux (UML) that builds whole network topologies — routers, switches, bridges, tunnels, VLANs — from plain-text configuration files, where every virtual machine is a lightweight UML instance sharing the host kernel. It supports IPv4/IPv6, QoS traffic shaping (the hierarchical token bucket qdisc) and netem impairment (configurable drops, duplicates, out-of-order delivery), which makes it suited to studying routing protocols without hypervisor overhead; Quagga (zebra/OSPF/BGP) example topologies ship with it. It was written by Catalin(ux) M. Boie and its real upstream is the author's site (kernel.embedromix.ro) — the GitHub repository listed above is only a third-party dump of the release tarball. Linux-only, and it requires uml_utilities.

## Typical use cases

### Validate a topology without booting it

```
dinouml newtop dump
```

Outcome: parses newtop/main.conf and the per-device newtop/*.conf files and prints the resolved topology — hosts, links, switches, addresses — without starting anything; configuration errors surface here before any machine boots.

### Boot the whole topology

```
sudo dinouml newtop run
```

Outcome: uses the default root filesystem image, prints "o Start switches..." and then "o Start machine <name>..." for every configured guest; each machine becomes reachable on the telnet port declared with port= in its .conf file, or through a tap interface configured in main.conf. Reference configurations ship under /usr/share/dinouml/cfg and /usr/share/dinouml/examples (e.g. ospf2, bgp-v4).

### Tear a topology down

```
dinouml newtop stop
```

Outcome: attempts to stop all UML switches and machines but warns "Not yet implemented reliably. You must manually clean all stuff." — expect to kill leftover guest processes and interfaces yourself.

*No corpus evidence — derived from upstream documentation.*
