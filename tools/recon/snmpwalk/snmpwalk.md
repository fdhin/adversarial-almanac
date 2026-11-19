---
name: "snmpwalk"
categories:
  - corpus-recon
homepage: "https://net-snmp.sourceforge.io/"
origin: "corpus"
github-repo: "net-snmp/net-snmp"
packaged-by: []
meta-checked: "2026-11-09"
github-stars: 499
github-pushed: "2026-11-07"
github-archived: false
github-issues: 355
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 908
gh-activity-checked: "2026-11-09"
---

# snmpwalk

> Walk an SNMP agent's MIB tree to enumerate a device — interfaces, routes, processes, services and configured strings.

## What it's for

snmpwalk (net-snmp) repeatedly issues GETNEXT/GETBULK requests starting at an OID and prints every OID/value pair below it — by default the entire tree the agent will serve. Given a valid community string it turns an SNMP agent into an information firehose: system description and location, network interfaces and addresses, routing tables, storage tables and, on net-snmp agents, running processes and custom "extend" commands whose command lines frequently hold credentials. The essential syntax is a version plus community for SNMPv1/v2c. A walk that returns nothing usually means a wrong community string or filtered UDP 161 rather than nothing to find — replay with other candidate strings before giving up.

## Typical use cases

### Walk the whole tree with the default community

First move against any SNMP-capable host.

```
snmpwalk -v2c -c public 198.51.100.10
```

Outcome: dumps every OID the agent serves; a timeout means the community is wrong or UDP 161 is filtered. The full dump is the starting point for the targeted walks below.

### Target a single subtree

Skip re-walking megabytes of interface data when hunting one table.

```
snmpwalk -v2c -c public 198.51.100.10 .1.3.6.1.4.1.2021.9
```

Outcome: returns only the requested subtree — here the UCD-SNMP disk table (mount points, capacities, usage); other proven subtree targets include 1.3.6.1.2.1.4.34 for IPv6-to-MAC neighbor mappings.

### Dump net-snmp "extend" scripts

Where custom monitoring commands are configured — often with credentials embedded.

```
snmpwalk -v2c -c public 198.51.100.10 nsExtendObjects
```

Outcome: prints the NET-SNMP-EXTEND-MIB objects (numeric OID .1.3.6.1.4.1.8072.1.3.2), including the command lines the agent's extend directives run; the same table is a command-execution path when the community string is writable.

### Fingerprint the device from sysDescr

```
snmpwalk -v2c -c public 198.51.100.10 1.3.6.1.2.1.1.1
```

Outcome: returns the system description string — OS, kernel and software versions — pinning exploit candidates before any port scan; agents that only speak SNMPv1 answer the same query when invoked with -v 1.

### Replay a discovered community string by hostname

When a guess or a leak yields a non-default community.

```
snmpwalk -v2c -c internal target.example.com
```

Outcome: a full walk authenticated with the recovered string — non-default communities often guard custom MIBs that "public" cannot read — and the agent can be named by DNS instead of IP.

### Read OIDs as short names

Make the flood of output greppable.

```
snmpwalk -Os -v2c -c public 198.51.100.10
```

Outcome: prints the MIB object name instead of the numeric OID (sysUpTime.0 = Timeticks: ...) so tables can be filtered by name in a pager or grep.

*Grounded in corpus command-line evidence (community-string variants, subtree and extend-table walks, short-OID output); flags verified against the net-snmp manual pages.*
