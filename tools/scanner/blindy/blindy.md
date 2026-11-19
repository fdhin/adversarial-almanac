---
name: "blindy"
categories:
  - blackarch-scanner
version: "12.59de8f2"
homepage: "https://github.com/missDronio/blindy"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "missDronio/blindy"
github-stars: 50
github-pushed: "2017-07-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# blindy

> Brute-forces blind SQL injection by replaying a query set into a vulnerable parameter or HTTP header and pattern-matching the responses.

## What it's for

Blindy automates the tedious half of blind SQL injection work: once you have found a parameter (or HTTP header) where injected input observably changes the response, it iterates a set of candidate queries (JSON "query sets", shipped in queries.json) through the `{}` placeholder and reports which ones landed. By default it looks for a negative pattern — text that disappears from the response when the injection succeeds — and `--positive` flips that to a pattern that must appear. GET, POST, and arbitrary headers work the same way. It only tells you which queries succeeded; extracting full datasets still needs a dedicated tool like sqlmap.

## Typical use cases

### Brute-force a POST parameter with a negative pattern

```
blindy http://target.example.com/login.php -X POST -p username={} -p submit=1 -r "Wrong username" -s "['login']"
```

Outcome: replays every query in the `login` set into `username`, reporting the ones where "Wrong username" vanished from the response.

### Switch to a positive pattern

```
blindy http://target.example.com/login.php -X POST -p username={} -p submit=1 -r "Welcome back, admin" --positive -s "['login']"
```

Outcome: same replay, but a query counts as successful only when the expected text IS present in the response.

### Brute-force a custom HTTP header

```
blindy http://target.example.com/index.php -p admin=1 -H "X-Custom_header: {}" -r "Wrong param" -s "['blind'][0]"
```

Outcome: injects into the header value instead of a parameter; the `[0]` index runs a single query from the set rather than all of it.

### Use your own query set

```
blindy http://target.example.com/index.php -p id={} -f myqueries.json -r "Error" -s "['mysql']" -e -v
```

Outcome: loads query sets from myqueries.json, URL-encodes the payloads (`-e`), and prints full request/response detail (`-v`).

*No corpus evidence — derived from upstream documentation.*
