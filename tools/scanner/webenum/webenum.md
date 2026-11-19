---
name: "webenum"
categories:
  - blackarch-scanner
version: "21.24b43b4"
homepage: "https://github.com/sarthakpandit/webenum"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "sarthakpandit/webenum"
github-stars: 0
github-pushed: "2016-03-14"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webenum

> HTTP response enumerator that fuzzes URLs, POST data and headers with dynamically generated query values.

## What it's for

WebEnum (the 2011-era Google Code tool, mirrored on GitHub) takes a URL, POST body or header template containing placeholder expressions and fires one request per generated value. Placeholders load wordlists (`[[wl:file]]`), generate integer ranges (`[[int:1:100]]`), character ranges (`[[char:aaa:zzz]]`) or SQL-injection NULL tables (`[[table:1:20]]`). Each response can be filtered through a Python-like match expression, so the tool doubles as a response-oracle finder (error strings, status codes, reflected content). It is a Python 2 script with a built-in wordlist of common web paths and writes results to a self-contained HTML report. Unlike a plain directory buster, it enumerates any HTTP parameter or header, not just paths.

## Typical use cases

### Fuzz a URL path or parameter from a wordlist

```
python2 webenum.py "http://target.example.com/[[wl:wordlist.txt]]"
```

Outcome: one request per wordlist entry; a progress summary prints and all results land in `results.html` (plus a plain log) in a per-run directory — open it to compare every response.

### Enumerate POST parameters with generated integer ranges

```
python2 webenum.py "http://target.example.com/query" -d "param=[[int:1:100]]"
```

Outcome: sends 100 POST requests with `param` counting 1..100, harvesting how the endpoint answers each value — useful for ID enumeration behind a form.

### Fuzz a header value

```
python2 webenum.py "http://target.example.com/" -h "Referer=[[wl:referers.txt]]"
```

Outcome: one request per referer string, everything else fixed — for testing header-dependent behavior such as redirect logic or spoofable validation.

### Keep only responses matching an oracle expression

```
python2 webenum.py "http://target.example.com/query?param=[[int:1:100]]" -m 'response.contains("correct login") and code != 404'
```

Outcome: the match expression filters every response (`contains`, `containsre` with regex, status `code`, `or`/`and`); only matching requests are logged as findings, turning raw enumeration into a yes/no probe result.

### Tune speed and thread count

```
python2 webenum.py "http://target.example.com/[[wl:wordlist.txt]]" -t 10
```

Outcome: runs the enumeration with 10 worker threads instead of the default 3, trading load on the target for faster completion.

*No corpus evidence — derived from upstream documentation.*
