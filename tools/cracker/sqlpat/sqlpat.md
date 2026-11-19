---
name: "sqlpat"
categories:
  - blackarch-cracker
version: "1.0.1"
homepage: "http://www.cqure.net/wp/sqlpat/"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-06"
build-system: other
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sqlpat

> Offline Microsoft SQL Server password auditor (dictionary and brute force) for sysxlogins password hashes.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Auditing the strength of Microsoft SQL Server passwords offline, without ever
touching the database server again. The tool (Patrik Karlsson's SQLPAT, whose
binary is named `sqlbf`) attacks the password hashes stored in the
`sysxlogins` table of the `master` database: retrieve them once with a
privileged account, save them as `user,hash` lines in a text file, and run
either a dictionary attack (`-d`) or a brute-force attack over a character
set file (`-c`, format shown by the bundled `default.cm`). Found matches are
written to a report file. It targets the SQL Server 7/2000 hash format (the
`0x0100`-prefixed strings from `sysxlogins`), not later versions, and being
2002-era C code its headline speed (~750k guesses/sec on a 1 GHz Pentium) is
far below modern hash crackers. The BlackArch package installs the binary as
`sqlbf`.

## Typical use cases

### Collect hashes into the expected file format

Pull the auditable material out of SQL Server.

```
SELECT name, password FROM master..sysxlogins;
```

Then write one `username,0x...` pair per line:

```
# hashes.txt
sa,0x0100905BB15ECA1847296A79ADD350E3138D6D255BF9FA24964FCA1847296A79...
user,0x01008444930543174C59CC918D34B6A12C9CC9EF99C4769F819B43174C59CC...
```

Outcome: the query (run as `sa` or another privileged login) returns each
login's name and password hash; saving them as comma-separated lines produces
the exact input file sqlpat expects, matching the bundled `hashes.txt`
sample.

### Dictionary attack against the hashes

Test a word list offline.

```
sqlbf -u hashes.txt -d dictionary.dic -r out.rep
```

Outcome: hashes every word in `dictionary.dic` against each entry in
`hashes.txt` and appends any recovered plaintext passwords to the report file
`out.rep` along with timing statistics.

### Brute-force with a character set file

Exhaust a keyspace when no word list applies.

```
sqlbf -u hashes.txt -c default.cm -r out.rep
```

Outcome: when `-d` is omitted, brute-force mode is assumed: every candidate
combinable from the characters in `default.cm` (a plain string such as
`ABCDEFGHIJKLMNOPQRSTUVXYZ0123456789`) is tried against each hash, with
matches reported to `out.rep`.
