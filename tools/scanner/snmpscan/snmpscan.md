---
name: "snmpscan"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://www.nothink.org/perl/snmpscan/index.php"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-09-05"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# snmpscan

> Multi-process Perl SNMP scanner: sweeps an address range for agents answering a community string and flags read/write access.

## What it's for

snmpscan.pl (Matteo Cantoni, goony@nothink.org, 2005) is a free multi-process SNMP scanner. It sends an SNMP v1 GET for the sysDescr OID over UDP/161 to every host in a range — around 200 parallel processes by default — and prints each responding host with the community that worked, an `r`/`w` access marker and the system description, finishing with a hosts-scanned summary. With `-w` it additionally probes whether the community permits write (SET) access, which turns the scan into a configuration-risk audit. It discovers and triages SNMP-enabled devices; it is not a full OID walker (pair it with snmpwalk/snmpenum for depth). The original nothink.org download location is offline; this reference is derived from the archived author page and the packaged script.

## Typical use cases

### Sweep a subnet for SNMP agents

```
snmpscan -t 198.51.100.0/24
```

Outcome: one line per responding host in the form `198.51.100.10 public ; r ; <sysDescr>`, plus a final summary of hosts scanned and elapsed time — a fast inventory of which devices expose SNMP and what they are.

### Find valid communities and write access

```
snmpscan -t 198.51.100.0/24 -c communities.txt -w -l
```

Outcome: retries the sweep with each community in the list, marks hosts that accept writes (`; w ;`) — devices that can be reconfigured via SNMP SET and are the highest-value findings — and logs the full run to `snmpscan.log`.

*No corpus evidence — derived from upstream documentation (archived nothink.org author page and packaged Perl source).*
