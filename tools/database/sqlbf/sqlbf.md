---
name: "sqlbf"
gentoo-category: "dev-db"
version: "1.0.1-r1"
homepage: "http://www.cqure.net/wp/sqlpat/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: make
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sqlbf

> Offline Microsoft SQL Server password auditor — the same tool as [[sqlpat]], filed under its binary name.

*No corpus evidence — derived from upstream documentation.*

## What it's for

This is cqure's SQLPAT/SQLBF tool documented under the name its binary
installs as. It audits the strength of Microsoft SQL Server (7/2000-era)
passwords offline against `sysxlogins` hashes, with either a dictionary
(`-d`) or a character-set brute force (`-c`) attack, reporting recovered
plaintexts to a file.

The full reference lives in [[sqlpat]]: the same upstream
(cqure.net, `sqlbf-all-src`) that BlackArch packages under the name
`sqlpat` — installing this same `sqlbf` binary — and this Pentoo package
under the name `sqlbf`. Use the [[sqlpat]] note for input file format,
commands, and report output under either name; the use cases are identical
and deliberately not duplicated here.

Related: the Pentoo ebuild builds the cqure C sources directly (bf.c,
crypto.c, sqlbf.c); there is no separate GitHub project for this tool.
