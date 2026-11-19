---
name: "peepingtom"
categories:
  - blackarch-webapp
version: "56.bc6f4d8"
homepage: "https://bitbucket.org/LaNMaSteR53/peepingtom"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Rainism/peepingtom"
github-stars: 13
github-pushed: "2014-06-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# peepingtom

> Screenshots many web servers quickly from a host list, producing a single browsable report for triage.

## What it's for

Peepingtom solves the triage problem after a port scan: given a list of web servers, it fetches each one, captures a screenshot (via PhantomJS or PyQt4), and builds one `report.html` so you can flip through what is actually running and pick targets worth deeper testing. Without a capture mode it still collects response headers. It was built to consume nmap output distilled through gnmap parsing, turning `IP:PORT` lines into a visual inventory. A Python 2 era tool; its Bitbucket home is gone (the linked GitHub repo is a mirror).

## Typical use cases

### Screenshot a single page

```
peepingtom -u http://target.example.com -p
```

Outcome: PhantomJS renders the page and a timestamped directory is created containing the screenshot plus a `report.html` linking it.

### Triage a list of servers from a scan

```
peepingtom -i http_ips.txt -p
```

Outcome: every `IP:PORT` line in the file is probed (the protocol is guessed, port 443 treated as HTTPS), captured, and rolled into one dated `report.html` — feed it hosts distilled from an nmap gnmap output for a quick visual inventory.

### Collect headers only, no browser

```
peepingtom -i http_ips.txt
```

Outcome: with no capture mode set the tool warns that it is retrieving header data only and records each target's HTTP response headers, which still fingerprints server software for triage.

No corpus evidence — derived from upstream documentation.
