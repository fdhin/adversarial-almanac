---
name: "mysql2sqlite"
categories:
  - blackarch-database
version: "14.e5b2c31"
homepage: "https://gist.github.com/esperlu/943776"
packaged-by: [blackarch]
blackarch-builddate: "2015-01-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "mysql2sqlite/mysql2sqlite"
github-stars: 1982
github-pushed: "2023-10-04"
github-archived: false
github-issues: 58
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mysql2sqlite

> awk script that rewrites a mysqldump file into SQLite3-compatible SQL so it can be piped straight into sqlite3.

## What it's for

Converts the MySQL-specific syntax of a `mysqldump` file into SQL that SQLite3 accepts: `ENGINE`/`CHARSET` table options are stripped, inline `KEY` definitions become separate indexes, `AUTO_INCREMENT` columns become `integer PRIMARY KEY AUTOINCREMENT`, `COMMENT` and `COLLATE` clauses are removed or rewritten, over-long hex literals are trimmed with a warning, and multi-row `INSERT` statements are handled. The intended input is a dump produced with `mysqldump --skip-extended-insert --compact`; the output is a SQLite dump that loads with the `sqlite3` shell. It converts dump files only — it never connects to a live MySQL server and is not a migration daemon. Warnings (trimmed hex values, identifier case collisions) go to stderr.

## Typical use cases

### Convert and load in one pipeline

```
mysql2sqlite dump_mysql.sql | sqlite3 mydb.sqlite
```

Outcome: builds `mydb.sqlite` with the converted schema and all rows — an InnoDB table with `AUTO_INCREMENT` and a multi-row `INSERT` comes out as a valid SQLite table that loads in a single transaction. A stray `memory` line on stdout from `PRAGMA journal_mode = MEMORY` is harmless.

### Convert without loading

```
mysql2sqlite dump_mysql.sql > dump_sqlite3.sql
```

Outcome: writes SQLite3-compatible SQL to stdout for inspection or hand-editing before loading; warnings still go to stderr. Called with no file argument it prints its usage block and exits 1.

*No corpus evidence — derived from upstream documentation.*
