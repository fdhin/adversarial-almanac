---
name: "oscanner"
categories:
  - blackarch-fuzzer
version: "1.0.6"
homepage: "http://www.cqure.net/wp/oscanner/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2024-12-16"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# oscanner

> Oracle database assessment scanner that enumerates SIDs, accounts, and password weaknesses against a live TNS listener.

## What it's for

oscanner (Oracle Scanner 1.0.6, by Patrik Karlsson of cqure.net) is a Java-based Oracle assessment framework with a plugin architecture. Against a reachable Oracle listener it enumerates SIDs, the database version, account roles, privileges, password hashes, audit settings, password policies, and database links, and tests both common and dictionary passwords (default `users.txt` and `dictionary.txt` lists ship with it). Findings are written to an XML report that the bundled reportviewer renders as a tree. It is an assessment scanner, not a fuzzer, and it needs a live listener to talk to before any plugin produces output.

## Typical use cases

### Scan a single Oracle host

```
oscanner -s 198.51.100.10 -r report.xml
```

Outcome: connects to the listener on the default port, runs all plugins (SID enumeration, password tests, account/role/privilege/hash/audit/policy/database-link enumeration) and writes the XML report to report.xml; add `-v` for verbose plugin output.

### Scan a list of servers on a custom port

```
oscanner -f servers.txt -P 1521 -v
```

Outcome: iterates over the hosts listed in servers.txt, scanning each listener on port 1521 with verbose per-plugin output; review the generated report afterwards.

### Browse a saved report

```
reportviewer report.xml
```

Outcome: opens the graphical Java tree viewer shipped alongside oscanner to walk through the enumerated findings from a finished scan.

*No corpus evidence — derived from upstream documentation.*
