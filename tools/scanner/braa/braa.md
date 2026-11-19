---
name: "braa"
categories:
  - blackarch-scanner
version: "0.82"
homepage: "http://s-tech.elsat.net.pl/braa/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2025-11-30"
build-system: make
license: GPL-1.0-only
meta-checked: "2026-11-09"
github-repo: "mteg/braa"
github-stars: 101
github-pushed: "2026-06-23"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# braa

> Mass SNMP scanner that queries hundreds of hosts from a single process using its own minimal SNMP stack.

## What it's for

Braa (by Mateusz 'mteg' Golicz) does what snmpget and snmpwalk do, but for dozens to hundreds of hosts at once — non-blocking sockets, a single process, and one packet per query type per host, which makes it dramatically faster and lighter than looping net-snmp. It implements its own deliberately minimal SNMP stack (no SNMP libraries needed; SNMPv1 packets, with `-2` presenting as SNMPv2C for Counter64 values), and it accepts only numerical OIDs — translate symbolic names with `snmptranslate` first. Host ranges, per-query communities, GET/WALK/SET, and output tags for scripting are all expressed in the query-list syntax.

## Typical use cases

### Single GET on one host

```
braa 198.51.100.10:.1.3.6.1.2.1.1.5.0
```

Outcome: fetches sysName.0 from the host with community `public` and prints `host:RTT:oid:value`, e.g. `198.51.100.10:25ms:.1.3.6.1.2.1.1.5.0:router.example.com`.

### Walk a subtree

```
braa 198.51.100.10:.1.3.6.1.2.1.1.*
```

Outcome: walks the whole `system` subtree like snmpwalk, one `host:RTT:oid:value` line per object returned.

### Sweep a range with a custom community

```
braa private@198.51.100.1-198.51.100.50:.1.3.6.1.2.1.1.6.0
```

Outcome: GETs sysLocation.0 from all 50 hosts concurrently with community `private`, printing answers as they arrive; unanswered hosts are retried per `-r` (default 3).

### Tagged multi-query output for scripting

```
braa 198.51.100.10:.1.3.6.1.2.1.1.4.0/Contact,.1.3.6.1.2.1.4.20.*/Routes
```

Outcome: sends both queries in a single packet and prefixes each output line with its tag (`Contact:...`, `Routes:...`) so awk or sort can split them; `-f queries.txt` reads query lists one per line instead, and appending `=sValue` to an OID performs a SET (type prefixes `s`, `i`, `a`, `o`).

*No corpus evidence — derived from upstream documentation (README of the packaged 0.82 source).*
