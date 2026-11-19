---
name: "wafninja"
categories:
  - blackarch-webapp
version: "26.ce6ea60"
homepage: "https://github.com/khalilbijjou/WAFNinja"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-24"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "khalilbijjou/WAFNinja"
github-stars: 834
github-pushed: "2026-08-11"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# wafninja

> CLI that automates WAF bypass work: fuzz which characters and keywords pass the filter, then replay known bypass payloads from a local database.

## What it's for

WAFNinja (Khalil Bijjou) helps penetration testers bypass a web application firewall by automating the steps of input-validation testing. It ships with a local database of fuzz strings and bypass payloads: the fuzz function probes which symbols and keywords the WAF lets through, and the bypass function replays stored payloads against a target parameter — together they answer "what syntax survives this WAF?". It supports GET and POST requests, cookies for authenticated pages, and routing through an intercepting proxy. The database is user-extendable (insert-fuzz / insert-bypass) and swappable (set-db), which makes it usable as a team-shared payload store. The packaged wrapper runs it under Python 2.

## Typical use cases

### Fuzz which characters and keywords the WAF allows

Map the filter first.

```
wafninja fuzz -u "http://target.example.com/index.php?id=FUZZ" -c "phpsessid=value" -t xss -o fuzz-output.html
```

Outcome: systematically substitutes fuzz strings for FUZZ in the URL and writes an HTML report of which symbols and keywords were allowed versus blocked; -c supplies a cookie for authenticated pages.

### Replay bypass payloads against a parameter

Then try the known evasions.

```
wafninja bypass -u "http://target.example.com/index.php" -p "Name=PAYLOAD&Submit=Submit" -t xss -o bypass-output.html
```

Outcome: sends each stored payload of the chosen type into the PAYLOAD slot of the POST body and reports which ones were not blocked, writing results to bypass-output.html.

### Extend the payload database

Grow the local store.

```
wafninja insert-fuzz -i select -e select -t sql
```

Outcome: adds the fuzz string with its explanation to the sql group of the local database, so future fuzz runs include it; insert-bypass does the same for bypass payloads, and set-db points the tool at a shared database file for team use.

No corpus evidence — derived from upstream documentation (README of khalilbijjou/WAFNinja).
