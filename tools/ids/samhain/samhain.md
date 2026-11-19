---
name: "samhain"
categories:
  - ids
version: "4.4.6"
homepage: "https://la-samhna.de/samhain/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# samhain

> File integrity and host-based intrusion detection system for Linux/UNIX, with baseline signatures, log/file tamper detection and central logging.

## What it's for

Samhain (Rainer Wichmann, la-samhna.de) is a mature host-based IDS built
around file integrity monitoring: it records cryptographic signatures
and attributes of the filesystem into a baseline database, then verifies
systems against that baseline. Beyond plain integrity checking it
monitors log files for tampering, watches its own configuration and
database, supports kernel-based port monitoring, and can operate as a
daemon for continuous checks with scheduling, stealth (signed database,
hidden daemon) options, and client/server operation where reports are
forwarded to a central log server. Everything is driven by the
`/etc/samhainrc` configuration file; the `-t` option selects the
operation mode. Development is active at la-samhna.de (no canonical
GitHub repository — the site distributes signed release tarballs).

## Typical use cases

### Create the baseline database from a known-good system

```
samhain -t init -p
```

Outcome: Samhain walks the filesystem per the `samhainrc` file sets,
computes signatures for each selected file, and writes the baseline
database. `-p` shows progress. The database should be built from a
verified-clean state, because everything recorded here becomes the
reference against which all later checks are compared (re-running
`-t init` twice detects a database that changed between runs).

### Verify a system against the baseline

```
samhain -t check
```

Outcome: Samhain recomputes signatures and attributes for everything in
the database and reports every file that was added, removed or modified
(new hashes, size, ownership/mode/timestamp changes) to the configured
log destinations — the core integrity audit. In `check` mode it will
not modify the database, so the report is a pure comparison.

### Update the baseline after approved changes

```
samhain -t update
```

Outcome: after reviewing the differences from a `-t check` run and
accepting the changes (e.g. a legitimate package upgrade), Samhain
merges the current state into the existing database instead of
rebuilding it from scratch, keeping the baseline current without
invalidating the history of unchanged files.

Without `-t`, Samhain neither initializes nor checks but performs its
other configured duties — in daemon mode it runs continuous monitoring
per the `[Daemon]`/schedule settings in `samhainrc`, forwarding reports
to the configured log file or central server.

*No corpus evidence — derived from upstream documentation.*
