---
name: "hulk"
categories:
  - blackarch-dos
version: "27.ed2b11c"
homepage: "https://github.com/grafov/hulk"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-26"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "grafov/hulk"
github-stars: 909
github-pushed: "2023-08-03"
github-archived: false
github-issues: 36
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hulk

> HULK (HTTP Unbearable Load King) ported to Go — floods a web server with unique, cache-busting requests from lightweight goroutines.

## What it's for

HULK exhausts a web server by making every request different: randomized URL paths, headers and user agents defeat caching layers, URL-pattern filters and connection-trimming heuristics, so the server must generate a fresh response per request until its workers or memory give out. This is the Go port (by grafov) of Barry Shteiman's original Python tool, keeping the same request-fuzzing logic but running connections as goroutines, which sustains a far larger connection pool on the same hardware than the threaded Python original. The author warns it can genuinely knock over a badly configured server or fragile app — point it only at systems you own.

### Flood a target site with unique requests

The stock run: GET requests that never repeat.

```
hulk -site http://target.example.com/ 2>/dev/null
```

Outcome: opens connections up to the pool limit (1,024 by default) and issues GET requests with randomized URLs and headers; request errors stream to stderr (discard them) while the target's traffic and connection counters climb until it stops responding or you interrupt the tool.

### Flood with POST bodies, a user-agent file and extra headers

Exercise an application's form-handling path instead of its static path.

```
hulk -site http://target.example.com/ -data 'q=searchterm' -agents agents.txt -header 'X-Forwarded-For: 198.51.100.10'
```

Outcome: switches to POST requests carrying the given data, rotates user agents from the file (one per line, overriding the built-in list) and adds the extra header to every request — the flood now stresses script execution and database queries rather than cacheable assets.

### Cap the connection pool and stop automatically

Protect the sending host and auto-halt when the target folds.

```
HULKMAXPROCS=256 hulk -site http://target.example.com/ -safe
```

Outcome: limits the connection pool to 256 so the flood does not exhaust the local file-descriptor table on smaller machines, and `-safe` shuts the tool down automatically once the target stops responding instead of erroring into the void.

*No corpus evidence — derived from upstream documentation.*
