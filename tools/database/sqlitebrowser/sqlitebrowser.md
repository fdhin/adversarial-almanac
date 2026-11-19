---
name: "sqlitebrowser"
categories:
  - database
  - forensics
  - web
version: "3.13.1-3"
homepage: "https://sqlitebrowser.org"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-02-24"
build-system: cmake
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "sqlitebrowser/sqlitebrowser"
github-stars: 24762
github-pushed: "2026-11-07"
github-archived: false
github-issues: 815
github-match: "search"
gh-checked: "2026-11-07"
worth: 9.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 142
gh-activity-checked: "2026-11-09"
---

# sqlitebrowser

> Visual editor for SQLite database files (DB Browser for SQLite).

## What it's for

DB Browser for SQLite (DB4S) is a GUI application for creating, browsing,
searching, and editing SQLite database files through a spreadsheet-like
interface — no SQL knowledge required. Reach for it whenever you hold a
`.db`/`.sqlite` file (application data, browser stores, tool databases,
forensic artefacts) and want to see its tables and rows immediately, run
ad-hoc queries in an Execute SQL tab, or export to CSV and SQL dumps. It
is primarily interactive: heavy automation belongs to the `sqlite3` CLI,
though the `sqlitebrowser` binary does accept a database path and a small
set of command-line flags for opening files read-only, jumping to a table,
running an SQL script, and importing CSV.

## Typical use cases

### Open a database in the GUI

Inspect an unknown SQLite file visually.

```
sqlitebrowser appdata.db
```

Outcome: launches the DB Browser window with the database already loaded —
the schema appears in the structure tab and every table's rows are viewable
and editable from the Browse Data tab, no SQL needed.

### Open a file without risk of modifying it

Protect originals and evidence from accidental edits.

```
sqlitebrowser -R evidence.db
```

Outcome: opens the database in read-only mode, so browsing, searching, and
querying cannot alter the original file.

### Start focused on one table

Skip clicking through the table list for a known table.

```
sqlitebrowser -t users appdata.db
```

Outcome: opens the database with the Browse Data tab already showing the
named table instead of the first table in the schema.

### Run a saved SQL script and exit

Use it from a script without sitting in the GUI.

```
sqlitebrowser -s export.sql -q appdata.db
```

Outcome: executes the statements in `export.sql` against the database right
after opening it, then (`-q`) quits instead of waiting for the user — the
script-friendly corner of an otherwise graphical tool.

### Import a CSV file

Bring spreadsheet data into a SQLite database.

```
sqlitebrowser --import-csv contacts.csv target.db
```

Outcome: runs the CSV import into the given database; if no database file
is passed, the CSV is imported into an in-memory database for quick
inspection.
