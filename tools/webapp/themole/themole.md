---
name: "themole"
categories:
  - blackarch-webapp
version: "0.3"
homepage: "http://sourceforge.net/projects/themole/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "tiankonguse/themole"
github-stars: 51
github-pushed: "2017-02-26"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# themole

> The Mole: an interactive console SQL injection exploiter for union and blind boolean injections.

## What it's for

The Mole (v0.3, originally distributed via SourceForge) is a command-line SQL injection
exploitation tool with an interactive shell. You give it a vulnerable URL (including
parameters) and a needle — a string that appears in the response when the injected query
is valid and is absent otherwise — and it exploits both union-based and blind boolean-based
injections. It supports MySQL, MSSQL, and PostgreSQL, and can walk schemas, tables, and
columns, then pull specific rows, all from its console. It does not find injections for you:
you must already have identified the injectable parameter. The original project is long
unmaintained; the vault's github-repo points at a third-party mirror of the same source.

## Typical use cases

### Start a session against a vulnerable parameter

Launch the console with the target URL and needle preset.

```
themole -u 'http://target.example.com/page.php?id=1' -n 'Welcome back'
```

Outcome: opens the interactive Mole prompt with URL and needle already set (`-u` and `-n` equal the `url` and `needle` console commands); `-t` sets the number of query threads at startup.

### Enumerate and dump through the console

Drive the injection interactively once a session is open.

```
url http://target.example.com/page.php?id=1
vulnerable_param GET id
dbinfo
schemas
tables mysql
columns mysql user
query mysql user User,Password where User = 'root'
```

Outcome: each command runs against the injected parameter — `dbinfo` returns current user, database name, and DBMS version; `schemas`/`tables`/`columns` walk the structure (cached after first fetch, `fetch` forces a refetch); `query` dumps the chosen columns with an optional `where` condition. Change request method with `method GET|POST`, and set basic auth with `auth`.

No corpus evidence — derived from upstream documentation.
