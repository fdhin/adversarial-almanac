---
name: "uatester"
categories:
  - blackarch-misc
version: "1.06"
homepage: "https://code.google.com/archive/p/ua-tester/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-12"
build-system: scripts
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "Seabreg/UAtester"
github-stars: 0
github-pushed: "2019-09-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# uatester

> Diffs one URL's responses across many User-Agent strings.

*No corpus evidence — derived from upstream documentation.*

## What it's for

UAtester (Chris John Riley, v1.06, 2010) is a Python 2 script for
user-agent-based differential testing: it first makes a reference request
with a plain browser User-Agent, then replays the request with every UA
string you supply (or its built-in categorized set) and diffs the responses
— status codes, redirect targets, response-body MD5 and length,
added/removed/altered server headers, and Set-Cookie changes. The point
during an authorized test is to see whether a WAF, filter, or theme switcher
serves different content to mobile devices, search bots, security tools or
hostile UAs. It fetches and compares only — flagged differences need manual
review.

## Typical use cases

### Sweep with the built-in UA categories

Quick pass across mobile, desktop, tool, bot and hostile user agents.

```
uatester -u http://target.example.com/ -d MDCTB -v
```

Outcome: replays the URL with the built-in sets for the chosen categories —
(M)obile, (D)esktop, mis(C), (T)ools, (B)ots, e(X)treme — and prints only
checks that differ from the reference connection: `[+]` added headers, `[-]`
removed, `[!]` altered, plus differing response codes, redirects, body
MD5/length and cookies; `-v` also shows full headers per check.

### Test a custom UA list and keep a CSV report

Use your own strings, one per line.

```
uatester -u http://target.example.com/ -f ua-list.txt -o results.csv
```

Outcome: replaces the built-in sets with the file's entries, prints the same
differential view per entry, and writes a CSV report of the checks to
`results.csv` (the file is overwritten) for later sorting and comparison.

### Probe one specific UA string

Check a single agent without building a list.

```
uatester -u http://target.example.com/ -s "Mozilla/5.0 (compatible; bot)" -v
```

Outcome: runs the reference request plus exactly that User-Agent and, in
verbose mode, prints the full headers of both so any header-level
difference is directly visible.
