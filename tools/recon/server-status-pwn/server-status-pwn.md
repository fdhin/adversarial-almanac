---
name: "server-status-pwn"
categories:
  - blackarch-recon
version: "12.841d55d"
homepage: "https://github.com/mazen160/server-status_PWN"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "mazen160/server-status_PWN"
github-stars: 445
github-pushed: "2021-09-19"
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

# server-status-pwn

> Exploit a world-readable Apache mod_status page: harvest every requested URL and every client IP, continuously.

## What it's for

When Apache's server-status handler is reachable by unauthenticated visitors (a misconfiguration, not an exploit), it leaks real-time request data for every vhost: hidden and obscure paths, files, and any tokens passed in query strings no matter what TLS is in play. server-status_PWN polls the page on an interval, diffs it against everything already seen, and stores each new URL and client IP — turning a passive disclosure into a monitoring feed for red-team intelligence or a PoC of severity. Findings land in a SQLite database, and unique URLs can also be written to a newline-delimited file for feeding other tools. It only reads what the page already exposes; it does not attack Apache itself.

## Typical use cases

### Monitor an exposed server-status page

```
python server-status_PWN.py --url 'http://target.example.com/server-status'
```

Outcome: repeatedly fetches the status page, printing each newly observed URL and source IP as traffic happens and storing them in a SQLite database — query the DB afterwards for hidden paths and GET-string tokens worth pursuing.

*No corpus evidence — derived from upstream documentation.*
