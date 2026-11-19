---
name: "xsscon"
categories:
  - blackarch-webapp
version: "45.ce91fd6"
homepage: "https://github.com/menkrep1337/XSSCon"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "menkrep1337/XSSCon"
github-stars: 249
github-pushed: "2019-09-01"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xsscon

> Simple crawler-based XSS scanner for GET and POST form parameters, written in Python 3.

## What it's for

XSSCon takes a URL, crawls the site's links (default depth 2), and tests the parameters it finds — URL variables and both GET and POST form fields, including hidden inputs — with generated XSS payloads (six built-in levels, or one custom payload). It runs checks across multiple processes, supports authenticated scanning via a cookie option, and can route traffic through a proxy or a custom user-agent. It explicitly does not test DOM-based XSS, and being unmaintained since 2019 it is best treated as a quick legacy check rather than a primary scanner. On BlackArch the wrapper script runs `xsscon.py`, so the packaged command is `xsscon`.

## Typical use cases

### Crawl and scan a site

Give it a base URL and let the crawler find testable parameters.

```
xsscon -u http://target.example.com
```

Outcome: XSSCon crawls links up to depth 2, tests GET and POST parameters against its default payload set, and prints findings as it goes — a first-pass sweep of a lab web app.

### Scan a single page without crawling

Skip the crawler and test one known-vulnerable-looking URL only.

```
xsscon --single http://target.example.com/search.php?q=test
```

Outcome: only the given address is fetched and its parameters tested, which avoids touching the rest of the site when one form is the target.

### Scan with a custom payload

Replace the built-in payload generator with a specific vector.

```
xsscon -u http://target.example.com --payload '<svg/onload=alert(1)>'
```

Outcome: every candidate parameter is tested with exactly the supplied payload instead of the generated set — handy to confirm a specific bypass string at scale.

### Scan behind authentication

Attach the session cookie so protected pages and forms are reachable.

```
xsscon -u http://target.example.com --cookie "{'SESSION':'abcdefgh123'}"
```

Outcome: requests carry the given cookie (Python-dict-style string, e.g. `{'ID':'1094200543'}`), letting the crawler and scanner reach login-only pages.

### Route the scan through a proxy

Send all scanning traffic through an intercepting proxy.

```
xsscon -u http://target.example.com --proxy "{'https':'http://198.51.100.10:8080'}"
```

Outcome: every request the scanner makes appears in the proxy on 198.51.100.10:8080 (e.g. Burp Suite), so payloads and reflected responses can be inspected live.

No corpus evidence — derived from upstream documentation.
