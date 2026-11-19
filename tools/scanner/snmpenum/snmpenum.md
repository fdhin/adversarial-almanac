---
name: "snmpenum"
categories:
  - blackarch-scanner
version: "1.7"
homepage: "http://www.filip.waeytens.easynet.be/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "ajohnston9/snmpenum"
github-stars: 6
github-pushed: "2018-06-13"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snmpenum

> Perl SNMP table dumper: feeds a community string and an OID list to an agent and prints everything it leaks.

## What it's for

snmpenum.pl (Filip Waeytens) is a simple SNMP enumerator: given an IP, a community string and an OID-list file it dumps each SNMP table named in the file — essentially a curated `snmpwalk` aimed at pentest enumeration. The package ships three OID lists under `/usr/share/doc/snmpenum/`: `linux.txt`, `windows.txt` and `cisco.txt`, covering system descriptions, interfaces, processes, software and similar. SNMP v2c agents only; empty output for a table usually means the MIB is unsupported or the community string is wrong. Custom OID files are plain text with tab-separated description, table name and OID, so the tool can be pointed at anything `snmpwalk` can reach.

## Typical use cases

### Dump SNMP tables from a Linux host

```
snmpenum 198.51.100.10 public /usr/share/doc/snmpenum/linux.txt
```

Outcome: for every OID table in `linux.txt` that the agent answers, prints the table's contents (system information, interfaces, mounted storage, running processes and the like) — an immediate inventory leak whenever the default community works.

### Enumerate a Cisco or Windows device

```
snmpenum 198.51.100.11 public /usr/share/doc/snmpenum/cisco.txt
snmpenum 198.51.100.12 public /usr/share/doc/snmpenum/windows.txt
```

Outcome: the same table dump using the vendor-specific OID lists — pick the file that matches the device class identified during earlier enumeration to pull vendor details (e.g. Cisco interface/memory tables, Windows software inventory).

*No corpus evidence — derived from upstream documentation (packaged README and Perl source).*
