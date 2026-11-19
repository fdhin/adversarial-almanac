---
name: "artillery"
categories:
  - blackarch-defensive
version: "1.5.1.r218.g805a5d8"
homepage: "https://www.trustedsec.com/downloads/artillery/"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-14"
build-system: scripts
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "BinaryDefense/Artillery"
github-stars: 1039
github-pushed: "2022-01-06"
github-archived: false
github-issues: 25
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# artillery

> Combined honeypot, file-integrity monitor, and brute-force alerting agent for Linux and Windows hosts.

## What it's for

Binary Defense's Artillery stacks several cheap defensive tripwires onto one host: it listens on a configurable set of decoy "honeyports" and permanently bans any IP that connects to them, watches chosen directories (by default `/etc` and `/var/www` on Linux) for file modifications, monitors logs for SSH and FTP brute-force attempts, and emails alerts when any of these trigger. Everything is configured in `/var/artillery/config` (monitored folders, honeyports, mail delivery, brute-force thresholds), and banned IPs accumulate in `/var/artillery/banlist.txt`. It is trip-and-alert defense for a single host — not a firewall policy manager and not centralized SIEM tooling.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Start the monitoring stack

Bring up honeyports, folder monitoring, and log watching together.

```
sudo python /var/artillery/artillery.py
```

Outcome: the agent loads `/var/artillery/config`, opens the decoy ports, and starts the folder and log monitors; any connection to a honeyport lands the source IP in `banlist.txt` plus a firewall drop rule, and alerts go to the configured email address. Run from the source tree before installation, the same command detects the missing install and launches `setup.py` first.

### Un-ban an address

Release a host that tripped a honeyport.

```
sudo python /var/artillery/remove_ban.py 198.51.100.10
```

Outcome: removes `198.51.100.10` from `banlist.txt` and deletes its matching firewall block rules — reach for it when a scanner, monitoring probe, or colleague legitimately touched a decoy port and needs to reach the host again.
