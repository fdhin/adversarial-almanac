---
name: "dbpwaudit"
categories:
  - blackarch-cracker
version: "0.8"
homepage: "http://www.cqure.net/wp/dbpwaudit/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/dbpwaudit"
github-stars: 0
github-pushed: "2019-05-04"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dbpwaudit

> Java tool that audits live database logins by attempting real JDBC connections with username and password lists.

## What it's for

DBPwAudit performs online password audits — guessing against running database services, not cracking hashes offline. It tries every combination from a username file and a password file by opening actual connections over JDBC, and reports each login that succeeds, making it a password-quality checker for database engines where lockout policies are also in scope. It ships preconfigured for Oracle, MySQL, Microsoft SQL Server (`MSSql`), and IBM DB2, but due to licensing it ships without the JDBC drivers themselves: vendor driver JARs must be present in its `jdbc/` directory, with the alias-to-driver mapping in `aliases.conf` (which also holds default ports and URL templates) and per-error-code handling in `rules.conf` — responses can be classified IGNORE, LOCKED, ABORT, or PRINT, so an Oracle `28000` account-locked error is recognized rather than misread, and a `17002` network failure aborts the run.

## Typical use cases

### See which database drivers are configured

```
dbpwaudit -L
```

Outcome: prints the driver aliases defined in aliases.conf — `Oracle`, `MySQL`, `MSSql`, `DB2` — which are the values `-D` accepts; if an alias is missing from the list, its driver JAR or mapping line is absent.

### Audit MySQL credentials with user and password lists

```
dbpwaudit -s target.example.com -d shop -D MySQL -U users.txt -P passwords.txt
```

Outcome: attempts every user/password pair as a real JDBC connection to the `shop` database on the default MySQL port (3306) and prints each working login; failed attempts are filtered through rules.conf so locked accounts are reported as locked rather than guessed further.

### Hit a service on a non-default port

```
dbpwaudit -s target.example.com -p 1434 -D MSSql -U users.txt -P passwords.txt
```

Outcome: same combination spray against SQL Server moved off its default port (aliases.conf defaults: Oracle 1521, MySQL 3306, MSSql 1433, DB2 50000); for Oracle and DB2 the `-d` option additionally names the SID or database in the connection URL.

*No corpus evidence — derived from upstream documentation.*
