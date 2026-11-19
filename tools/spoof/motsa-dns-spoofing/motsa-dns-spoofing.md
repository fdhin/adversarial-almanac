---
name: "motsa-dns-spoofing"
categories:
  - blackarch-spoof
version: "2.6ac6980"
homepage: "https://github.com/waytoalpit/ManOnTheSideAttack-DNS-Spoofing"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "waytoalpit/ManOnTheSideAttack-DNS-Spoofing"
github-stars: 5
github-pushed: "2016-05-12"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# motsa-dns-spoofing

> Man-on-the-side DNS injector — sniffs A queries on the wire and races forged replies, with an optional per-domain answer file (packaged as `motsa-dnsinject`).

## What it's for

A small scapy demonstration of a man-on-the-side attack against DNS: instead of proxying traffic, it passively watches UDP/53 and fires a spoofed positive answer at the client the moment a query appears, hoping to beat the legitimate resolver. Without a config file every queried name is answered with the attacking machine's own interface IP — a crude phishing redirect. With a config file, chosen domains resolve to chosen addresses while everything else still falls back to your IP. It is Python 2 only and needs root to sniff and send raw packets. BlackArch wraps the `dnsinject.py` script as the `motsa-dnsinject` command; run the script directly from an unpacked repo the same way.

## Typical use cases

### Answer every DNS query with your own IP

```bash
sudo motsa-dnsinject -i eth0
```

Outcome: every A-record query seen on `eth0` gets a forged reply (TTL 120) resolving the name to eth0's IP address, and `Packet created and sent!` is printed per answer. Victims' traffic for any name they look up now points at your machine.

### Redirect only selected domains via an answer file

```bash
sudo motsa-dnsinject -i eth0 -f dns.ini
```

Outcome: names listed in `dns.ini` under its `[database]` section (one `example.com = 198.51.100.10` pair per line) resolve to the configured address; any name not in the file still falls back to your interface IP.

### Limit the attack to specific victim IPs

```bash
sudo motsa-dnsinject -i eth0 -f dns.ini 198.51.100.77,198.51.100.78
```

Outcome: the trailing comma-separated list acts as a source filter — only queries originating from those IPs are answered, leaving the rest of the LAN's resolution untouched.

*No corpus evidence — derived from upstream documentation.*
