---
name: "f-scrack"
categories:
  - blackarch-cracker
version: "19.9a00357"
homepage: "https://github.com/ysrc/F-Scrack"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ysrc/F-Scrack"
github-stars: 308
github-pushed: "2017-09-05"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# f-scrack

> Single-file, stdlib-only Python brute-forcer for weak passwords over FTP, MySQL, MSSQL, MongoDB, Redis, Telnet, Elasticsearch and PostgreSQL.

*No corpus evidence — derived from upstream documentation.*

## What it's for

F-Scrack (ysrc, Python 2) is a lightweight weak-password ("weak password") checker: one script, no third-party libraries, every protocol implemented with plain sockets. Give it hosts and an optional password dictionary and it pings for live hosts, matches open ports to the services it speaks, and tries the dictionary against each. That makes it a quick first-pass audit tool for default credentials across a subnet — not a heavy-duty cracker: no rule mutation, no GPU work, and the protocol set stops at the eight services listed above. The upstream repository has since been emptied by its author (an anti-misuse deletion notice remains), so the packaged version and the final published README are the reference; flags below reflect that last documentation.

## Typical use cases

### Dictionary-check a /24-style range

Sweep a subnet for the eight supported services with your wordlist.

```
f-scrack -h 198.51.100 -d passwords.txt
```

Outcome: ping-scans the range for live hosts, then attempts every password in `passwords.txt` against each supported service found on the default port set (21, 23, 1433, 3306, 5432, 6379, 9200, 11211, 27017), printing each weak credential found. `-h` also accepts a single IP (`198.51.100.10`), an explicit range (`198.51.100.1-198.51.100.254`), or a file of IPs (one per line, up to 65,535 targets per run).

### Target specific services with tuned threading

Restrict the port set and control speed on a known range.

```
f-scrack -h 198.51.100.1-254 -p 3306,5432 -m 200 -t 6
```

Outcome: scans only MySQL (3306) and PostgreSQL (5432) on each host in the range, using 200 threads (default 100) and a 6-second per-request timeout, reporting any account/password from the default dictionary that authenticates.

### Scan an IP list without ping discovery

Skip liveness detection when ICMP is blocked or slow.

```
f-scrack -h targets.txt -n
```

Outcome: reads `targets.txt` and goes straight to connecting on the default ports without the ICMP pre-scan (`-n`), useful on networks that filter ping; each discovered weak login is printed as it succeeds.
