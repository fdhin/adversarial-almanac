---
name: "admsnmp"
categories:
  - blackarch-scanner
version: "0.1"
homepage: ""
packaged-by: [blackarch]
blackarch-builddate: "2024-11-24"
build-system: other
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# admsnmp

> 1999 ADM-crew SNMPv1 auditor: brute-forces community names and then tests each working one for WRITE access to the MIB.

*No corpus evidence — derived from upstream documentation (ADMsnmp 0.1 as packaged by BlackArch; flags verified against snmp.c).*

## What it's for

admsnmp (ADMsnmp) is a single-file C scanner for SNMPv1 agents. It walks a wordlist of candidate community strings, notes which ones the agent actually answers, and — its distinguishing trick — sends a set-request with each valid community to determine whether it grants write access to the MIB, reporting lines like `name = private write access`. Candidate names can also be generated from the target's hostname instead of a wordlist. It speaks raw SNMPv1 over UDP, predates SNMPv3 entirely, and ships with a default wordlist installed at `/usr/share/admsnmp/snmp.passwd`. Its option parser matches prefixes, so `-wo`, `-wor` and `-wordfile` are all accepted spellings of the wordlist flag. A readable community is an information leak; a writable one is full device control — confirm both with modern tooling and treat findings as critical.

## Typical use cases

### Brute-force SNMP community names

```
admsnmp 198.51.100.10 -wor /usr/share/admsnmp/snmp.passwd
```

Outcome: Prints a trace of get-requests sent and packets received, then a summary block listing every community the agent accepted and whether write access was attained (e.g. `name = private write access`) — feed a working read community to `snmpwalk` to dump the tree.

### Guess community names from the hostname

```
admsnmp target.example.com -g
```

Outcome: In addition to the wordlist, candidates are derived from the target's reverse-resolved hostname (the name itself plus numeric and case variants), catching lazy conventions where the device's own name is its community string.

### Bound the wait and record findings

```
admsnmp 198.51.100.10 -wor /usr/share/admsnmp/snmp.passwd -sleep 2 -out found.txt
```

Outcome: The scan gives up after 2 seconds of silence from the agent (`-sleep`, default 10) instead of hanging on a dead or filtered UDP port, and every valid community name is written to the output file as it is confirmed.
