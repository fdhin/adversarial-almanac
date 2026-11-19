---
name: "dsss"
categories:
  - blackarch-webapp
version: "128.13002c3"
homepage: "https://github.com/stamparm/DSSS"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "stamparm/DSSS"
github-stars: 882
github-pushed: "2026-10-14"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# dsss

> A fully functional SQL injection vulnerability scanner (supporting GET and POST parameters) written in under 100 lines of code.

## What it's for

DSSS (Damn Small SQLi Scanner) is Miroslav Stampar's SQL-injection scanner in under 100 lines of Python: point it at a URL and it tests each GET (or POST, via `--data`) parameter with boolean/error-based SQL injection probes and reports parameters that respond differently under injected conditions. Reach for it when you want a dependency-free, fully auditable SQLi triage pass — a quick "is anything obviously injectable" answer — rather than the depth of SQLmap; it identifies, it does not extract data. Proxy and `Cookie`/`User-Agent`/`Referer` headers cover authenticated or proxied targets. The BlackArch package installs the script as `/usr/bin/dsss`.

## Typical use cases

### Test a GET parameter for SQL injection

The canonical run against a URL with a candidate parameter.

```
dsss -u "http://target.example.com/product.php?id=1"
```

Outcome: probes the `id` parameter with injection payloads and prints findings such as `GET parameter 'id' appears to be blind SQLi vulnerable` including a proof-of-concept URL, ending with `scan results: possible vulnerabilities found` on success.

### Test a POST parameter

Target a form field instead of the query string.

```
dsss -u "http://target.example.com/search.php" --data="query=test"
```

Outcome: runs the same injection probes against the POST parameter named in `--data`, reporting `POST parameter 'query' appears to be ... vulnerable` in the same output shape.

### Scan through a proxy with a session

Inspect or authenticate the scan traffic.

```
dsss -u "http://target.example.com/product.php?id=1" --proxy "http://127.0.0.1:8080" --cookie "session=abc123" --user-agent "Mozilla/5.0"
```

Outcome: sends every probe through the intercepting proxy with the cookie and User-Agent applied, so authenticated endpoints can be tested and every request reviewed in Burp.

No corpus evidence — derived from upstream documentation (README sample runs and options).
