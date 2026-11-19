---
name: "mssqlscan"
categories:
  - blackarch-scanner
version: "0.8.4"
homepage: "http://www.cqure.net/wp/mssqlscan/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# mssqlscan

> Discover live Microsoft SQL Server instances on a network by parsing their UDP responder replies.

## What it's for

MSSQLScan (Patrik Karlsson, Java) is a small multi-threaded scanner that finds Microsoft SQL Server instances by probing the SQL Server UDP responder and parsing what comes back. For each detected instance it reports the host name, instance name, version and the connection endpoints (TCP port and named pipe), which is exactly what you need before pointing a client or an attack tool at an instance. The original cqure.net upstream is gone; the packaged release is the final 0.8.4, wrapped by BlackArch as the `mssqlscan` command around the bundled launcher script (requires a Java runtime).

No corpus evidence — derived from upstream documentation (archived cqure.net tool page).

## Typical use cases

### Find SQL Server instances across a range

Sweep a subnet for live MSSQL instances.

```
mssqlscan -t 198.51.100.*
```

Outcome: UDP-probes every matching address and prints one result line per instance in the form `ip;HOST;INSTANCE;VERSION;[np:\\HOST\pipe\sql\query, tcp:1433]`, listing both named-pipe and TCP endpoints.

### Scan a target file and save results

Work from a prepared list with tuned threading and timeouts.

```
mssqlscan -l targets.txt -T 100 -w 1000 -o results.txt
```

Outcome: reads one IP per line from targets.txt, runs up to 100 threads (default 50) with a 1 s reply timeout (default 500 ms), and writes the instance report to results.txt; `-v` adds verbose logging to the console.
