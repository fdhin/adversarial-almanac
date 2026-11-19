---
name: "asleap"
categories:
  - blackarch-cracker
version: "2.5"
homepage: "https://github.com/OscarAkaElvis/asleap"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-09-26"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "OscarAkaElvis/asleap"
github-stars: 39
github-pushed: "2026-09-12"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 54
gh-activity-checked: "2026-11-09"
---

# asleap

> Recovers weak Cisco LEAP and PPTP (MS-CHAPv2) passwords from captures or live wireless sniffing.

## What it's for

asleap (originally by Joshua Wright, maintained since 2026 by OscarAkaElvis) attacks the MS-CHAPv2 challenge-response exchanges used by Cisco LEAP wireless authentication and PPTP VPNs. It reads handshakes from a libpcap capture or sniffs them live on a monitor-mode interface, then cracks each exchange against precomputed NT-hash lookup files built by the bundled genkeys tool — the lookup is indexed on the last two bytes of the NT hash, so even large dictionaries stay fast. Any MS-CHAPv2 exchange that lands in your capture is fair game, not just LEAP.

## Typical use cases

### Build the NT-hash lookup files

```
genkeys -r words.txt -f hash.dat -n index.dat
```

Outcome: hashes every word in the dictionary into hash.dat with a companion index index.dat; asleap loads this pair for fast lookups while cracking.

### Crack a capture

```
asleap -r capture.pcap -f hash.dat -n index.dat
```

Outcome: extracts LEAP/PPTP exchanges from the pcap, attacks each against the lookup files, and prints the username and recovered password for every broken exchange.

### Sniff and attack live

```
asleap -i wlan0mon -f hash.dat -n index.dat
```

Outcome: captures on the monitor-mode interface and cracks LEAP handshakes as they appear, reporting recovered credentials in real time.

### Crack straight from an ASCII wordlist

```
asleap -r capture.pcap -W words.txt
```

Outcome: skips the precomputed hash files and tests the capture's exchanges directly against the plain wordlist — simpler to set up but slower on large dictionaries.

*No corpus evidence — derived from upstream documentation.*
