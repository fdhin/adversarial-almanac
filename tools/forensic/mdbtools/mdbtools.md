---
name: "mdbtools"
categories:
  - blackarch-forensic
version: "738.823b32f"
homepage: "https://github.com/mdbtools/mdbtools"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2020-05-22"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "mdbtools/mdbtools"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 1186
github-pushed: "2026-11-09"
github-archived: false
github-issues: 29
github-commits-year: 58
gh-activity-checked: "2026-11-09"
---

# mdbtools

> Command-line utilities for reading Microsoft Access (.mdb) JET databases — list tables, export data to CSV/SQL/JSON and dump schema without Windows, Access or ODBC.

*No corpus evidence — derived from upstream documentation.*

## What it's for

MDB Tools is a set of programs to extract data from Microsoft Access files, written by Brian Bruns. The utilities read .mdb files (JET 3 and JET 4 formats) directly through the bundled libmdb, so neither Microsoft Access nor Windows is needed to get at the data. In forensic work it is the standard way to triage Access databases found on seized systems or in legacy web-application backups: enumerate the tables, locate credential or logging tables, and export their contents as CSV or SQL for analysis. It is read-only tooling — it extracts and converts, but does not modify or create Access databases.

## Typical use cases

### List the tables in an Access database

```
mdb-tables -1 backup.mdb
```

Outcome: prints every user table name, one per line. Piping through `grep -iE 'user|auth'` surfaces candidate credential tables — Access-backed web apps commonly keep usernames and password hashes in a table such as auth_user. Add `-S` to also list system tables (MSys*).

### Export a table as CSV

```
mdb-export backup.mdb auth_user > auth_user.csv
```

Outcome: writes the table's rows to stdout as quoted CSV with a header row (saved here to auth_user.csv), ready for a spreadsheet or database — e.g. it reveals the username and password columns of a web app's user table.

### Export a table as SQL INSERT statements

```
mdb-export --insert sqlite backup.mdb auth_user > auth_user.sql
```

Outcome: emits INSERT statements in the chosen SQL dialect (access, sybase, oracle, postgres, mysql or sqlite) instead of CSV, so the recovered table can be loaded straight into an SQL database for querying.

### Dump the schema of the database

```
mdb-schema backup.mdb
```

Outcome: prints CREATE TABLE DDL for the database's tables, documenting its structure for the report; restrict to one table with `-T tablename`, or append a backend name to emit the DDL in that dialect.

### Identify the JET version of a file

```
mdb-ver backup.mdb
```

Outcome: prints whether the file is JET 3 (Access 97) or JET 4 — which determines charset handling (MDB_JET3_CHARSET) and which other parsers will accept the file.

### Run SQL queries against the database

```
mdb-sql backup.mdb
```

Outcome: opens an interactive SQL shell over the database's tables (e.g. `SELECT * FROM auth_user;`), for targeted extraction without exporting whole tables.
