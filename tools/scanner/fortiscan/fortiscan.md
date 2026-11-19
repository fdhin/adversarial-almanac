---
name: "fortiscan"
categories:
  - blackarch-scanner
version: "0.7.r7.gd54faa0"
homepage: "https://github.com/anasbousselham/fortiscan"
packaged-by: [blackarch]
blackarch-builddate: "2022-02-20"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "anasbousselham/fortiscan"
github-stars: 160
github-pushed: "2023-06-30"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fortiscan

> Anas Bousselham's Go tool for CVE-2018-13379: checks FortiGate SSL-VPN endpoints for the pre-auth path traversal, and on vulnerable devices can pull cleartext credentials using the device serial number.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Fortiscan targets one specific, widely exploited weakness: the FortiOS SSL-VPN path traversal (CVE-2018-13379 / FG-IR-18-384) that lets an unauthenticated attacker read session files via a crafted `/remote/fgt_lang` request. Give it a host:port and it reports whether the endpoint is vulnerable; from version 0.7 it can also turn a successful traversal into cleartext credentials by pairing the leaked session data with the device's serial number. Affected firmware per the upstream README: FortiOS 6.0.0-6.0.4, 5.6.3-5.6.7 and 5.4.6-5.4.12. It is a point checker with an exploitation follow-through, not a general Fortinet scanner — configuration backups, SSH, admin interfaces and other FortiOS CVEs are out of scope.

## Typical use cases

### Check a single FortiGate SSL-VPN

```
fortiscan 198.51.100.10:10443
```

Outcome: probes the SSL-VPN on the given port (443, 10443 and 8443 are the typical ones) and reports vulnerable or not; on a hit the tool follows through with what the traversal exposes, including credential material on 0.7 when the device serial is recoverable.

### Audit a list of endpoints

```
fortiscan targets.txt
```

Outcome: iterates over the file list (host:port per line) and reports per endpoint — the bulk form for reviewing a fleet of appliances for unpatched SSL-VPN portals before anything else touches them.
