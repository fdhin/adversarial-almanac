---
name: "ocs"
categories:
  - blackarch-scanner
version: "0.2"
homepage: "http://packetstormsecurity.com/files/119462/OCS-Cisco-Scanner.2.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: other
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ocs

> OCS (Cisco Scanner): a compact C mass-scanner that sweeps an IP range for Cisco routers still carrying factory default telnet/enable credentials.

## What it's for

OCS is a 2000s-era one-trick tool: given an IP range, it connects to TCP/23 on every address, tries the Cisco default login (`cisco`), enters enable mode with the default enable password, and reports which devices accepted them. Multithreaded (built with `-lpthread`), it is meant for auditing whether an organization's router fleet still ships with factory credentials. Only use it on networks you are authorized to audit — logging into devices with guessed credentials is an active, logged event. The upstream is a PacketStorm-hosted C file (no project repo; the `github-repo` field previously pointed at an unrelated same-named academic-software project and was removed); the distro package compiles `OCS-0.2.c` and installs it as `ocs-cisco`.

## Typical use cases

### Sweep a range for default-credentialed routers

```
ocs-cisco 198.51.100.1 198.51.100.254
```

Outcome: scans every address between the start and end IP for an open telnet service, attempts the default login/enable sequence, and prints a line per hit (`|Default Telnet and Enable Passwords found! Vulnerable Router IP: ...`) — every printed IP is a device to remediate.

### Audit a single host

```
ocs-cisco 198.51.100.10 198.51.100.10
```

Outcome: same default-credential check against one device (start and end IP identical); a clean exit with no vulnerability lines means the router did not accept the factory credentials.

*No corpus evidence — derived from upstream source (OCS-0.2.c from dl.packetstormsecurity.net: usage strings, port 23 probe and credential sequence; install name `ocs-cisco` from the BlackArch PKGBUILD).*
