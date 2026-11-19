---
name: "dns-spoof"
categories:
  - blackarch-spoof
version: "13.81ba29f"
homepage: "https://github.com/maurotfilho/dns-spoof"
packaged-by: [blackarch]
blackarch-builddate: "2020-06-27"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "maurotfilho/dns-spoof"
github-stars: 32
github-pushed: "2020-06-23"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dns-spoof

> A small libpcap-based C utility that watches an interface for a specific DNS request and fires a forged reply mapping the requested name to an IP of your choice — plus a companion detector for spotting such spoofing.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A one-shot, single-query spoofer from a university network-security lab project (Mauro Tardivo Filho and classmates, UNICAMP): you name the interface, the query string and the answer you want, and the tool answers any matching lookup on the wire before the real resolver can. There is no rule file, no matching engine and no daemon behavior — one process handles one request pattern, which makes it handy for controlled demonstrations of DNS spoofing mechanics rather than engagement tooling. The package builds two binaries: `dns-spoof` (the spoofer) and `dns-spoof-detect` (a passive listener that flags suspicious DNS responses, e.g. multiple different answers for the same query). Needs root for packet capture.

## Typical use cases

### Answer one name with a chosen IP

One sentence of intent: redirect victims querying a specific hostname to a host you control.

```
sudo dns-spoof --interface eth0 --request www.example.com --ip 198.51.100.10
```

Outcome: listens on `eth0`; the next DNS query for `www.example.com` seen on the segment gets a forged reply resolving it to `198.51.100.10`, racing the legitimate resolver. All three options are required — the tool exits with a usage message if any is missing.

### Detect DNS spoofing on the wire

One sentence of intent: verify in a lab whether injected answers are observable.

```
sudo dns-spoof-detect --interface eth0
```

Outcome: passively captures DNS traffic on `eth0` and reports when responses look spoofed (such as conflicting answers for the same request); useful as the defender-side counterpart when testing the spoofer above, and to confirm your injected replies actually look suspicious to a monitor.
