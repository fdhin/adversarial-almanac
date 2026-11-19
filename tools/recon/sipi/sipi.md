---
name: "sipi"
categories:
  - blackarch-recon
version: "13.58f0dcc"
homepage: "https://github.com/ST2Labs/SIPI"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "ST2Labs/SIPI"
github-stars: 23
github-pushed: "2018-12-24"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sipi

> IP reputation triage — check an address (or a whole list) against threat-intel sources for a behavior verdict.

## What it's for

SIPI ("Simple IP Information") is an incident-response helper by ST2 Labs that takes one suspicious IP or a file of them and reports what open threat-intelligence sources say: blacklist categories such as malware, botnet, spam, phishing, blacklist and dnsbl, exposure data from Shodan (ports, banners), and geolocation/ASN data from ipinfo. API tokens go into a config.json next to the script. Caveat: its primary reputation feed, cymon.io, is no longer reachable, so the category lookups largely fail today — the Shodan and ipinfo lookups are the working parts.

## Typical use cases

### Full report on one address

```
python sipi.py 198.51.100.10 -A
```

Outcome: queries every configured source for the address and prints a combined reputation/exposure/geo report.

### Screen a list of addresses

```
python sipi.py ips.txt -t spam -s -i
```

Outcome: checks each address in the file against the spam blacklist category (-t) and appends Shodan exposure (-s) and ipinfo (-i) data per address.

### Widen the event window and result cap

```
python sipi.py ips.txt -t malware -d 4 -l 1000
```

Outcome: searches malware events beyond the default three-day window (-d 4 switches to blacklist-event mode) and scans up to 1000 feed entries per address (-l) instead of the default 100.

*No corpus evidence — derived from upstream documentation.*
