---
name: "metacoretex"
categories:
  - blackarch-database
version: "0.8.0"
homepage: "http://metacoretex.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# metacoretex

> Java-based vulnerability scanning framework for databases (SecurityCentric, 2003) that runs JDBC-driven probes against Oracle, MS SQL, MySQL and more from a Swing GUI.

## What it's for

MetaCoretex is an entirely Java scanning framework that puts special emphasis on databases: it connects to a target through any JDBC type-4 driver and runs probe objects against it. Probes share a per-target knowledge base — including live connection objects — so many can identify the engine version without knowing the database type in advance. The distribution ships probe logic plus data files such as default-account lists for Oracle (`conf/oracle_accounts.txt`) and dangerous MS SQL stored procedures (`conf/mssql_procs.txt`). Configuration, scanning and reporting all happen in a Swing GUI; both scan configurations and reports are saved as XML. The project is long dead — its online update server is gone — so use it as the frozen 0.8.0 release, driven from the GUI rather than the command line.

## Typical use cases

### Launch the scanner GUI

```
metacoretex
```

Outcome: starts the MetaCoretex Swing GUI (the packaged wrapper runs `mctx.sh`, which starts `java com.securitycentric.metacoretex.Init`). From there you open Window → Probe Config to enable probes and set their options, enter the JDBC connection details for the target database, run the scan while per-thread progress appears in the Scan Status window, and save results via File → Save as an XML report that can be reloaded later.

*No corpus evidence — derived from upstream documentation.*
