---
name: "spaf"
categories:
  - blackarch-webapp
version: "11.671a976"
homepage: "https://github.com/Ganapati/spaf"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Ganapati/spaf"
github-stars: 26
github-pushed: "2014-04-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spaf

> Static PHP analyzer plus fuzzer: map every user-controlled entry point of a local PHP app, then fuzz each one while watching the error log.

## What it's for

spaf (Static Php Analysis and Fuzzer, Ganapati, 2014) is a recon helper for testing a PHP
application you have locally: it statically walks the source folder, lists every user-controlled
input (GET/POST parameters) per script and line number, and — optionally — fuzzes each entry
point with random printable data while tailing the PHP error log, correlating triggered
warnings/fatals with the parameter that caused them. It deliberately does not exploit anything:
it prepares pentesting by charting attack surface. It is a Python 2-era tool (the packaged
launcher runs it under python2) and expects the `-d` folder to correspond to the `-u` URL — i.e.
the same app served over HTTP.

## Typical use cases

### List all entry points of a local application

```
spaf -d /var/www/app/ -r
```

Outcome: a static scan prints every PHP file with user-controlled inputs found, one per line
with its method and source line (e.g. `[GET] line 54 : f_sid`) — a complete input inventory for
scoping the audit. Recursive descent is enabled by `-r`.

### Fuzz every entry point and collect triggered errors

```
spaf -d /var/www/app/ -u http://localhost/app/ -r -f -l /var/log/apache2/error_log
```

Outcome: after the static pass, random data is sent to each parameter (`-n` controls the number
of strings per input); responses are logged per endpoint/parameter with the payload sent, and
any PHP errors correlated from the given log file are attached to the finding — highlighting
inputs that reach fragile code.

### Fuzz with a session and JSON output

```
spaf -d /var/www/app/ -u http://localhost/app/ -r -f -c "PHPSESSID=Password1&logged=true" -o json > findings.json
```

Outcome: the fuzzing run authenticates its requests with the supplied cookies and the report is
emitted as JSON (default is `pretty` text), ready for diffing between runs.

No corpus evidence — derived from upstream documentation (README).
