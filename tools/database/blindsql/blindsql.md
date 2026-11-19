---
name: "blindsql"
categories:
  - blackarch-database
version: "1.0"
homepage: "http://www.enye-sec.org/programas.html"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# blindsql

> Two bash scripts by Pepelux (enye-sec.org), `sqlcheck` and `sqldata`, that pull MySQL data out of a web page through quote-free blind SQL injection, using lynx and a TRUE/FALSE marker string.

## What it's for

Both scripts exploit a URL parameter that is injectable but returns no database errors. They append quote-free boolean expressions such as `(SELECT SUBSTRING(Database(),1,1)=CHAR(52))>0` to the vulnerable URL, fetch the response with lynx, and check whether a marker string — text that only appears when the injected condition is true — is still present. Values are recovered one character at a time. `sqlcheck` fingerprints the MySQL server (version, database, current users) and brute-forces table and column names from `tables.txt`/`fields.txt` wordlists (auto-created with common candidates if missing); `sqldata` extracts actual values from a known table and column, for example the password for a known username. Requires lynx, and only works when the page's output measurably differs between true and false conditions.

## Typical use cases

### Fingerprint the database server

```
sqlcheck "http://target.example.com/show.php?id=5" "Hello:" -m
```

Outcome: recovers and prints the MySQL `Version()`, `Database()` and `User()` values character by character, based on whether the marker string `Hello:` appears after each injected condition.

### Full verbose fingerprint

```
sqlcheck "http://target.example.com/show.php?id=5" "Hello:" -v -f
```

Outcome: prints verbose progress while recovering the full value set — `Version()`, `Database()`, `User()`, `System_user()`, `Session_user()`, `Current_user()` and `Connection_id()`.

### Brute-force table and column names

```
sqlcheck "http://target.example.com/show.php?id=5" "Hello:"
```

Outcome: without `-m`/`-f`, tests each candidate table from `tables.txt` and column from `fields.txt` with `(SELECT COUNT(*) FROM table)>0`-style probes, and logs confirmed names to `check-<host>-<date>.txt` in the working directory.

### Dump every value of a column

```
sqldata "http://target.example.com/show.php?id=5" "Hello:" users user
```

Outcome: recovers every `user` value stored in the `users` table, one character per boolean injection.

### Extract a linked column value

```
sqldata "http://target.example.com/show.php?id=5" "Hello:" users user admin pass
```

Outcome: recovers the `pass` value for the row where `user` equals `admin` — the pattern for pulling password hashes once one valid username is known.

*No corpus evidence — derived from upstream documentation.*
