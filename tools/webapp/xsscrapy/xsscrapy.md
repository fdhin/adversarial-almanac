---
name: "xsscrapy"
categories:
  - blackarch-webapp
version: "153.4966255"
homepage: "https://github.com/DanMcInerney/xsscrapy"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "DanMcInerney/xsscrapy"
github-stars: 1739
github-pushed: "2024-06-13"
github-archived: false
github-issues: 38
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xsscrapy

> Scrapy-based spider that crawls a whole site and tests every discovered input for XSS (plus error-based SQLi).

## What it's for

xsscrapy is a fast XSS spider built on the Scrapy framework: given one URL it crawls the site and tests each link's cookies, User-Agent, Referer, URL variables, end-of-URL, URL path, and forms (hidden fields included) for cross-site scripting — upstream advertises 66/66 detection on the WAVSEP XSS benchmark. Because dangerous XSS characters overlap with SQL-injection indicators, it also flags SQL errors in responses (using w3af's error-message list), but it does not test for blind SQLi. It supports authenticated crawling (form login or HTTP Basic Auth), custom cookies, concurrency and rate limiting. It is Python 2 era software (the packaged wrapper runs python2), so treat it as a legacy whole-site sweeper; findings are written to `xsscrapy-vulns.txt` in the working directory.

## Typical use cases

### Spider a site for XSS and SQLi errors

One command to crawl and test everything reachable without a login.

```
xsscrapy -u http://example.com
```

Outcome: the spider crawls the target, testing every discovered input; confirmed findings (URL, parameter, payload) are appended to `xsscrapy-vulns.txt`, and error-based SQLi indications are reported alongside the XSS hits.

### Crawl behind a form login

Start from the login page and supply the account so the crawler reaches authenticated pages.

```
xsscrapy -u http://example.com/login -l user
```

Outcome: the crawl proceeds as the given user, so member-only pages and their forms/parameters are tested too; on BlackArch the wrapper activates the package venv before launching the script.

### Crawl behind HTTP Basic Auth

Use Basic Auth credentials instead of a form login.

```
xsscrapy -u http://example.com/login -l user --basic
```

Outcome: every request carries the Basic Auth header, letting the spider cover areas protected by HTTP-level authentication.

### Bring an existing session

Paste a session cookie instead of performing a login.

```
xsscrapy -u http://example.com --cookie "SessionID=abcdef1234567890"
```

Outcome: the crawl runs with the supplied cookie attached to all requests, testing pages reachable under that session without needing credentials at all.

### Tune speed and stealth

Cap simultaneous connections or rate-limit the crawl against fragile targets.

```
xsscrapy -u http://example.com -c 20 -r 60
```

Outcome: at most 20 simultaneous connections are kept open, and requests are throttled to at most 60 per minute, trading scan speed for a lighter footprint on the target.

No corpus evidence — derived from upstream documentation.
