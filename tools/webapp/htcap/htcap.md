---
name: "htcap"
categories:
  - blackarch-webapp
version: "155.a59c592"
homepage: "https://github.com/segment-srl/htcap"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "segment-srl/htcap"
github-stars: 628
github-pushed: "2021-10-11"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# htcap

> SPA-aware web application crawler: drives headless Chrome to capture the XHR/fetch/websocket traffic classic crawlers never see, then chains scanners and reports on it.

## What it's for

htcap is built around a recursive DOM crawling engine that runs a single-page application in headless Chrome (Puppeteer), triggering events and intercepting every ajax/fetch/JSONP/websocket request plus DOM changes — a complete request map that href-only crawlers cannot produce. Findings land in an SQLite database and can be replayed through built-in fuzzers or external scanners (sqlmap, Arachni, Wapiti), with an interactive HTML report as the final artifact. Login sequences are scriptable and cookies/proxies/HTTP auth are supported for authenticated crawling. Its three commands (`crawl`, `scan`, `util`) are chainable with `\;` and share one database.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Crawl an SPA into a database

Capture the app's real request surface.

```
htcap crawl https://example.com htcap.db
```

Outcome: headless Chrome walks the app in the default aggressive mode (forms filled and submitted), writing every discovered request — XHR, fetch, JSONP, websockets — to `htcap.db`; Ctrl-C pauses the crawler or changes verbosity mid-run.

### Chain crawl, scan and report in one run

From first page load to report without intermediate steps.

```
htcap crawl https://example.com htcap.db \; scan sqlmap \; util report htcap.html
```

Outcome: after crawling, the sqlmap scanner replays captured requests looking for SQL injection, then an interactive HTML report of all findings is written to `htcap.html` (`scan -h` lists the available scanners).

### Bound the crawl's scope and interaction level

Crawl politely or precisely.

```
htcap crawl -m passive -s directory https://www.example.com/app/ app.db
```

Outcome: crawls without interacting with the page beyond loading it (`-m passive`; `active` triggers events, `aggressive` also fills forms), restricted to `/app/` and its subdirectories (`-s domain|directory|url`; `-d *.example.com` adds allowed domains), writing to `app.db`.

### Replay captured requests as curl commands

Export the crawl for other tooling or manual re-testing.

```
htcap util tocurl htcap.db > requests.sh
```

Outcome: emits every request in the database as a runnable curl command (an optional SQL WHERE clause filters which), preserving method, data, referer and cookies.
