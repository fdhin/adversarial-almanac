---
name: "unicornscan"
categories:
  - blackarch-scanner
version: "0.4.43"
homepage: "https://github.com/robertelee78/unicornscan"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2026-01-22"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "robertelee78/unicornscan"
github-stars: 31
github-pushed: "2026-03-27"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 546
gh-activity-checked: "2026-11-09"
---

# unicornscan

> Asynchronous, stateless TCP/UDP/ARP scanner with its own userspace TCP/IP stack and an optional PostgreSQL-backed web UI.

## What it's for

Unicornscan (created by Jack C. Louis; this repository is Robert E. Lee's maintained continuation) is a reconnaissance scanner built on a distributed userspace TCP/IP stack: sending and reply collection run as separate processes, letting it scale to very high packet rates with precise per-phase pacing. One tool covers TCP SYN/FIN/ACK, TCP connect, UDP, ARP discovery and traceroute modes, plus compound modes that chain them, with per-phase rate/repeat/timeout control, output modules such as OS fingerprinting and PostgreSQL logging, the fantaip helper that answers ARP for phantom IPs during connect scans, and the Alicorn web UI for browsing scan results. The `us` alias is installed next to `unicornscan`.

## Typical use cases

### SYN scan a subnet on chosen ports

```bash
us -p 22,80,443 198.51.100.0/24
```

Outcome: TCP SYN scan at a conservative default 250 pps printing `TCP open <ip>:<port> ttl <n>` lines; append `:a` to a target for all 65535 ports or `:q` for the top-256 quick list (per-target ports can also be attached individually, as in `198.51.100.10:22,443`).

### Fast full-range scan

```bash
us -r10000 198.51.100.0/16:a
```

Outcome: all 65535 ports at 10,000 packets per second — raise `-r` stepwise on networks proven to take the load.

### UDP scan with protocol payloads

```bash
us -mU -p 53,123,161 198.51.100.10
```

Outcome: sends protocol-specific UDP payloads (defined in `/etc/unicornscan/payloads.conf`) and reports responding UDP services.

### Compound ARP + TCP discovery on a LAN

```bash
us -mA+T 198.51.100.0/24
```

Outcome: the first phase ARP-discovers live hosts and the second SYN-scans them; per-phase tuning such as `-mA50:R3:L15+sf` sets 50 pps, three repeats and a 15 s listen window for the ARP phase, then connect scanning.

### Stream results immediately with OS detection

```bash
us -Iv -eosdetect 198.51.100.10
```

Outcome: results print as they arrive instead of buffering, with the osdetect output module adding fingerprint guesses; `-U` shows raw TCP flag states (e.g. `-S--A---` for SYN+ACK) instead of open/closed labels.

*No corpus evidence — derived from upstream documentation (robertelee78/unicornscan README and docs/GETTING_STARTED.md; BlackArch PKGBUILD v0.4.43 with Alicorn web UI).*
