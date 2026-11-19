---
name: "httpgrep"
categories:
  - blackarch-webapp
version: "4.8"
homepage: "https://nullsecurity.net/tools/scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "noptrix/httpgrep"
github-stars: 36
github-pushed: "2026-10-13"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 24
gh-activity-checked: "2026-11-09"
---

# httpgrep

> Async HTTP(S) scanner that greps response bodies and headers for strings or regex across hosts, ports, CIDR ranges and TLS-certificate vhosts.

## What it's for

httpgrep (nullsecurity) answers "which of my many web endpoints match X?": it accepts single hosts, URLs, CIDR/IP ranges or files of either, scans multiple ports per target (single, lists, ranges — auto-detecting TLS per port), optionally pulls name-based vhosts straight from TLS certificates as extra targets, and streams every match live to the terminal while logging to text/CSV/JSONL. It is engineered for large sweeps — thousands of concurrent connections, a cheap TCP preflight that skips dead ports, per-host and global timeouts, and resumable interrupted runs. It greps what services expose; it does not exploit anything.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Grep a single host

The one-liner form: does this server mention the string anywhere?

```
httpgrep -h example.com -s apache
```

Outcome: fetches the root path over auto-detected HTTP/HTTPS and prints any line of the response headers or body matching `apache`, with context bytes around body matches.

### Sweep a CIDR for a service signature

Find every instance of a technology across a network range.

```
httpgrep -h 198.51.100.0/24 -p 8080 -s tomcat -w body
```

Outcome: scans every address in the range on port 8080, searching response bodies only (`-w`; default is `headers,body`) for `tomcat`, and prints each matching host as it is found.

### Check specific paths across many hosts

Look past `/` for admin and login surfaces.

```
httpgrep -h example.com -t -u /admin,/login,/dashboard -s admin -i
```

Outcome: forces TLS (`-t`), requests each of the three URIs in turn, and reports case-insensitive matches for `admin` in the responses.

### Log matches for a big scan and resume it

Keep results and survive interruptions on wide sweeps.

```
httpgrep -h targets.txt -s /tmp/strings.txt -x 200 -l /tmp/out -O csv,jsonl -W
```

Outcome: reads hosts from `targets.txt` and strings/regex from the file, runs 200 concurrent connections, writes matches to `out.csv` and `out.jsonl`, and on Ctrl-C saves progress to `httpgrep.session` — rerun with `-W` to resume where it stopped.

### Filter out dynamic error pages

Cut false positives from custom 404/soft-error bodies.

```
httpgrep -h 198.51.100.0/24 -s admin -i -S 'error|not found' -I
```

Outcome: any response whose headers or body match the inverted pattern (`error|not found`, case-insensitive) is dropped entirely, so matches reflect real admin surfaces rather than boilerplate error pages.
