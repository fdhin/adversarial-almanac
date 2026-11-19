---
name: "blisqy"
categories:
  - blackarch-webapp
version: "20.e9995fc"
homepage: "https://github.com/JohnTroony/Blisqy"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "JohnTroony/Blisqy"
github-stars: 413
github-pushed: "2019-03-24"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# blisqy

> Find and exploit time-based blind SQL injection in HTTP headers (MySQL/MariaDB).

## What it's for

Blisqy targets the case where a web app passes an HTTP header (User-Agent, X-Forwarded-For, Referer, ...) into a MySQL/MariaDB query: it fuzzes every header from its wordlist with time-based payloads, then exploits a confirmed injection to siphon data character-by-character using bitwise operations on printable ASCII, driven by `sleep()` delays. Timing comparisons are computed dynamically at runtime to compensate for network lag, and tests run on greenlets rather than strictly sequentially. It supports MySQL/MariaDB only. Blisqy has no command-line interface: it ships as two editable Python scripts plus a library — `FindBlindSpot.py` (fuzzer) and `ExploitBlindSpot.py` (exploiter), which import `lib/blindfuzzer.py` and `lib/blindexploit.py`. On BlackArch the tree is installed at `/usr/share/blisqy`.

## Typical use cases

### Fuzz every HTTP header for time-based blind SQLi

Edit the target parameters at the top of the fuzzer script, then run it.

```
cd /usr/share/blisqy && python2 FindBlindSpot.py
```

Outcome: before running you set `Server`, `Port`, `Index` (delay) and the `fuzz-data/` header/payload files inside the script; it then fires the time-based payload list (e.g. `' or sleep(1)#`) at each header and prints a report marking injectable headers (`[!] Test N is Injectable`) with their timing records — proof-test positives manually before exploiting.

### Exploit a confirmed header injection

Configure `ExploitBlindSpot.py` with the vulnerable header, a template such as `' or if((*sql*),sleep(*time*),0) and '1'='1`, and exploitation options, then run it.

```
cd /usr/share/blisqy && python2 ExploitBlindSpot.py
```

Outcome: with `mysqlDig` enabled the `MysqlDigger()` routine enumerates databases, tables and columns through the injection; `interactive` lets you jump to the parts you care about, and a custom `payload` option (e.g. `select @@hostname`) runs an arbitrary query the same way.

The `lib.blindfuzzer.blindSeeker` and `lib.blindexploit.SqlEngine` modules can also be imported directly into other Python tooling, which is the intended integration path.

No corpus evidence — derived from upstream documentation and source.
