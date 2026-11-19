---
name: "snmpcheck"
categories:
  - blackarch-networking
version: "1.9"
homepage: "http://www.nothink.org/perl/snmpcheck/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "hacktrackgnulinux/snmpcheck"
github-stars: 3
github-pushed: "2022-12-30"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snmpcheck

> Human-readable SNMP enumerator in the spirit of snmpwalk, by Matteo Cantoni (nothink.org).

## What it's for

snmpcheck queries a device over SNMP v1 or v2c with a community string and prints everything it can read — system details, network interfaces, routing, services, mounted storage, TCP/UDP listener tables, running software — as an organized, friendly text report instead of a raw OID walk. It covers snmpwalk-style enumeration but formats the result for reading, and adds a write-access probe for finding misconfigured communities. It is enumeration-only: no flooding, no attacks. The same script is packaged as `snmpcheck` on Kali and `snmpcheck-nothink` on BlackArch.

## Typical use cases

### Enumerate a device with the default community

```
snmpcheck 198.51.100.10
```

Outcome: prints a structured report of everything readable via community `public` — hostname, description, interfaces, routes, services, listening TCP/UDP ports — the standard first look at an SNMP-exposed host.

### Target a specific community, port and version

```
snmpcheck -c private -p 161 -v 2c 198.51.100.10
```

Outcome: the same enumeration over SNMPv2c on the given port with the supplied community; a full report confirms the string works, an error or empty result suggests it does not.

### Check for a writable community

```
snmpcheck -w 198.51.100.10
```

Outcome: after enumerating, runs write-access detection and reports whether the community can set values — a finding that escalates SNMP exposure from information leak toward device reconfiguration.

*No corpus evidence — derived from upstream documentation.*
