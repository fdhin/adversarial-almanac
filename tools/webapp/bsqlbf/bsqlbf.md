---
name: "bsqlbf"
categories:
  - blackarch-webapp
version: "2.7"
homepage: "http://code.google.com/p/bsqlbf-v2/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "aramosf/bsqlbf"
github-stars: 5
github-pushed: "2013-08-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bsqlbf

> Perl blind SQL injection brute-forcer that extracts data one character at a time.

## What it's for

bsqlbf ("Blind SQL Injection Brute Forcer", v2.7 by aramosf, modified by sid@notsosecure) takes a single vulnerable URL and enumerates data through true/false-based blind SQL injection, brute-forcing each character of the query result. Beyond basic MySQL/MS-SQL/Postgres extraction it has specialised Oracle modes (types 3–8) that abuse `dbms_export_extension`, `DBMS_REPCAT_RPC` and related packages for OS command execution and file reads, plus timer-based (-time) inference and proxy/user-agent randomisation support. It is a Perl script from the early 2010s: single-threaded workflow, one URL at a time, no wrapper libraries. The packaged script prints its full option list with `-help`.

## Typical use cases

### Extract a value through a blind injection

Point it at an injectable parameter and give it the query to run.

```
bsqlbf -url http://target.example.com/page.php?id=1000 -blind id -sql "select table_name from information_schema.tables limit 1 offset 0" -database 1 -type 1
```

Outcome: `-database 1` selects MySQL and `-type 1` uses true/error-based inference; the tool brute-forces the result string character by character (`-match` pins the true-response marker, otherwise it is auto-detected) and prints the recovered value; `-start`/`-length` bound the extraction when the prefix is known.

### Read a file from the database server

When the DB user can read files, pull one through the injection.

```
bsqlbf -url "http://target.example.com/bug.php?r=514&p=foo'" -method post -get "/etc/passwd" -match "foo"
```

Outcome: the trailing quote in the URL marks the injection as string-based, `-method post` sends the request as POST, and the file contents are enumerated through the blind channel instead of a query result.

### Throttle and route the attack

Spread requests over time and through a proxy to stay quiet.

```
bsqlbf -url http://target.example.com/page.php?id=1000 -blind id -sql "(select @@version)" -database 1 -time 1 -proxy=http://127.0.0.1:8080 -uagent "Mozilla/5.0"
```

Outcome: `-time 1` waits 15 seconds per inference request (0 = no wait, 2 = 5 minutes), while `-proxy`/`-rproxy` and custom or randomised user-agents (`-uagent`, `-ruagent`) route each request through your interception proxy or a rotating list.

No corpus evidence — derived from the packaged upstream script's usage output.
