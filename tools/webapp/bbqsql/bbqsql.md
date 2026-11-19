---
name: "bbqsql"
categories:
  - blackarch-webapp
version: "261.b9859d2"
homepage: "https://github.com/neohapsis/bbqsql"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "neohapsis/bbqsql"
github-stars: 835
github-pushed: "2019-01-04"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bbqsql

> Blind SQL injection exploitation framework from Neohapsis — paste your custom injection query with placeholders into an interactive menu, and it searches each character out at gevent-driven concurrency.

*No corpus evidence — derived from upstream documentation (README plus the packaged menu sources).*

## What it's for

For blind SQLi that general-purpose tools can't finish — weird filter rules, banned characters, broken ASCII/CHAR functions — you normally end up writing a one-off extraction script. BBQSQL replaces that script: it is database-agnostic, takes whatever custom query syntax you've already proven works, and automates only the search part. You describe the HTTP request (URL with an `${injection}` marker, method, headers, cookies, POST data, proxy, auth) and the extraction query containing placeholders; BBQSQL then determines each row and character using binary search (default) or English-frequency search, deciding true/false from a response attribute you choose (status code, size, time, cookies…). Concurrency via gevent (default 30 workers, 75+ against fast servers) makes it notably quicker than hand-rolled loops. It is a legacy Python 2 project (v1.1, last upstream activity 2019) — expect to run it on an old interpreter or container, but the placeholder-driven workflow still handles injection quirks that break bigger tools.

## Typical use cases

### Configure and run an attack interactively

```
bbqsql
```

Outcome: opens the numbered menu — `1` sets the HTTP parameters (URL containing the `${injection}` marker, method, headers, cookies, data, proxies, auth), `2` sets the BBQSQL options (extraction query, `technique`, `comparison_attr`, `concurrency`, CSV output file), `5` runs the exploit once the configuration validates; results print to the console and optionally to a CSV. Option `99` exits.

### Replay or adjust an attack from a config file

```
bbqsql -c attack.cfg
```

Outcome: loads a previously exported configuration straight into the menu, validated like a hand-entered one, so an interrupted attack resumes — or only the query gets edited — without re-keying every option. A working config looks like:

```
[Request Config]
url = http://target.example.com/item?id=1${injection}
method = GET

[HTTP Config]
query = ' and ASCII(SUBSTR((SELECT user() LIMIT 1 OFFSET ${row_index:1}),${char_index:1},1))${comparator:>}${char_val:0} #
technique = binary_search
comparison_attr = size
concurrency = 30
```

The query placeholders drive the search: `${row_index}` iterates rows, `${char_index}` the character position, `${comparator}` the comparison operator, `${char_val}` the candidate value, and an optional `${sleep}` marks where to insert the delay for time-based attacks. Set `comparison_attr` to whatever actually differs between true and false responses (status_code, size, time, cookies, …), and `technique` to `frequency_search` when the data is English-like and non-entropic.

### Mutate every request with a hooks file

```
# hooks.py
def pre_request(req):
    req.url = req.url.replace('PLACEHOLDER', 'custom-encoded-value')
    return req
```

Outcome: with `hooks_file` pointing at this file in the BBQSQL options menu, the function runs on every request just before it is sent (sibling hooks: `args` before the request is built, `post_request` and `response` afterwards) — the escape hatch for targets that demand custom encoding or signing the generic tooling can't express.
