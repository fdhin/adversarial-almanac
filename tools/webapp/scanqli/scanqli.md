---
name: "scanqli"
categories:
  - blackarch-webapp
version: "26.40a028d"
homepage: "https://github.com/bambish/ScanQLi"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "bambish/ScanQLi"
github-stars: 202
github-pushed: "2019-07-15"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# scanqli

> Simple SQL injection detection scanner for URLs — classic, blind and time-based — without exploitation.

## What it's for

ScanQLi tests URL parameters for SQL injection by mutating them with
detection payloads for classic (error-based), blind and time-based SQLi. It
deliberately stops at detection: it will not dump data or exploit a finding,
which makes it a quick triage tool before reaching for sqlmap on the
confirmed parameters. It can recurse through every link found on the target
site, carry session cookies for authenticated pages, pace requests with a
wait delay, and skip URLs you list. Point it at a single URL with
parameters; it is a 2019-era python script and sees no active development.

## Typical use cases

### Scan a single parameterized URL

Test one endpoint and keep a log.

```
scanqli -u 'http://target.example.com/page/?p=news' -o output.log
```

Outcome: injects detection payloads into the `p` parameter and reports each
finding with its type (classic/blind/time-based); the full trace is written
to output.log.

### Recursive authenticated scan with cookies

Walk the whole site carrying a session.

```
scanqli -u 'http://target.example.com/app/' -r -c '{"PHPSESSID":"4bn7uro8qq62ol4o667bejbqo3"}'
```

Outcome: follows every href under the base URL (`-r`), tests each
parameterized link with the PHPSESSID cookie attached, and prints an
inventory of injectable parameters across the site.

*No corpus evidence — derived from upstream documentation (bambish/ScanQLi README usage and examples; installed as `/usr/bin/scanqli` running scanqli.py under python3).*
