---
name: "pgdbf"
categories:
  - blackarch-database
version: "113.4e84775"
homepage: "https://github.com/kstrauser/pgdbf"
packaged-by: [blackarch, aur]
blackarch-builddate: "2018-01-21"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "kstrauser/pgdbf"
github-stars: 146
github-pushed: "2020-06-18"
github-archived: false
github-issues: 23
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pgdbf

> Convert XBase/FoxPro .dbf tables into a SQL stream PostgreSQL imports directly.

*No corpus evidence — derived from upstream documentation.*

## What it's for

PgDBF converts XBase database files — particularly FoxPro tables with memo
files — into SQL that PostgreSQL can import as-is: it emits a
transaction-wrapped script containing `DROP TABLE IF EXISTS`, a
`CREATE TABLE` with mapped column types, a bulk `COPY` of every row, and
optional `CREATE INDEX` statements. You pipe its output into `psql`; it
never talks to PostgreSQL itself. It reads only (no PostgreSQL-to-DBF
direction) and handles the common XBase types including memo, currency,
timestamp and varchar fields.

## Typical use cases

### Load a DBF table into PostgreSQL

Convert and import in one pipe.

```
pgdbf table.dbf | psql targetdb
```

Outcome: prints the full import script — wrapped in a transaction so other
clients never see a half-loaded table — which `psql` executes, leaving
`table.dbf`'s contents as a same-named PostgreSQL table.

### Convert a FoxPro table with memo fields

Pull long text out of the companion .fpt file.

```
pgdbf -m table.fpt table.dbf | psql targetdb
```

Outcome: memo data stored in the external `.fpt` file is merged into
`TEXT` columns of the imported table. Memo file names are never guessed,
so any table with memo fields must name the file via `-m`.

### Convert a table stored in a legacy encoding

Keep non-ASCII data intact in a UTF-8 database.

```
pgdbf -s latin1 table.dbf | psql targetdb
```

Outcome: converts the table's bytes from the named encoding to UTF-8
(using iconv) during conversion, so accented or non-Latin characters
survive the import instead of failing it.

### Create indexes while importing

Land the table ready to query.

```
pgdbf table.dbf id "substr(name,1,4)"
```

Outcome: appends `CREATE INDEX` statements — one for each column or SQL
expression listed after the file name — so the imported table arrives with
usable indexes. Run the same command without piping to `psql` first to
inspect the generated SQL on stdout.
