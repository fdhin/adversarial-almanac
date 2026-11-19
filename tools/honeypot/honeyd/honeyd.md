---
name: "honeyd"
categories:
  - blackarch-honeypot
version: "337.a0f3d64"
homepage: "https://github.com/DataSoft/Honeyd/"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-16"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "DataSoft/Honeyd"
github-stars: 397
github-pushed: "2023-05-20"
github-archived: false
github-issues: 31
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# honeyd

> The classic low-interaction honeypot daemon that simulates entire networks of virtual hosts — personalities, ports and proxied services included — on a single machine.

## What it's for

Honeyd, written by Niels Provos (2002-2007) and kept buildable by DataSoft's fork, creates virtual hosts on a network: one daemon claims any number of IP addresses (its author tested up to 65536) and answers for them — ICMP ping and traceroute as well as arbitrary simulated TCP/UDP services defined in a configuration file. Personality engines driven by fingerprint files make each virtual host respond like a specific operating system to scanners such as nmap, and any service can instead be proxied to a real machine for deeper interaction. Reach for Honeyd when the goal is breadth — portraying a whole subnet of targets to map who probes your space and what they look for — rather than deep attacker engagement. It builds against libevent, libdnet and libpcap, and the upstream recommends running it sandboxed (chroot/systrace).

## Typical use cases

### Simulate a network of decoy hosts

Answer for an entire address range described by a config file.

```
sudo honeyd -f honeyd.conf 198.51.100.0/24
```

Outcome: honeyd daemonizes and responds to traffic routed to it for 198.51.100.0/24; connections, probes and fingerprinting attempts against those addresses are handled by the simulated hosts and logged according to honeyd.conf, so one machine portrays 254 fake targets.

### Run in the foreground with privilege dropping

Keep honeyd attached to the terminal for debugging while shedding root.

```
sudo honeyd -d -f honeyd.conf -u 65534 -g 65534 198.51.100.0/24
```

Outcome: honeyd stays in the foreground (`-d`) printing its logs to stdout, creates its raw sockets as root, then drops to uid/gid 65534 for the simulation loop — the shape recommended while tuning a new config before unattended deployment.

*No corpus evidence — derived from upstream documentation.*
