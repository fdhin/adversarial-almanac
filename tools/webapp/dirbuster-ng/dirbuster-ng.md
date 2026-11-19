---
name: "dirbuster-ng"
categories:
  - blackarch-webapp
version: "9.0c34920"
homepage: "https://github.com/digination/dirbuster-ng"
packaged-by: [blackarch]
blackarch-builddate: "2015-03-25"
build-system: cmake
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "digination/dirbuster-ng"
github-stars: 358
github-pushed: "2020-07-19"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dirbuster-ng

> C command-line reimplementation of the DirBuster web content brute-forcer.

## What it's for

dirbuster-ng reproduces OWASP DirBuster's directory and file brute-forcing in a small C binary built on libcurl: it walks a wordlist against a target URL, optionally appending extensions, with configurable threads, timeout, user agent, HTTP authentication and proxy support (including proxy auth and predefined browser/crawler user agents). It embeds a default dictionary and ships the DirBuster wordlists alongside (packaged under /usr/share/dirbuster-ng/wordlists/). Discovery only — it reports paths and response codes, not vulnerabilities.

## Typical use cases

### Scan with the embedded dictionary

```
dirbuster-ng https://example.com
```

Outcome: brute-forces the target with the built-in wordlist, printing discovered paths and their response codes as it goes.

### Custom dictionary with extensions

```
dirbuster-ng -d wordlist.txt -e php,html https://example.com
```

Outcome: loads your wordlist, appends each listed extension to every word, and scans the target with the expanded list.

### Threaded scan with results saved to a file

```
dirbuster-ng -w 20 -W results.txt -q https://example.com
```

Outcome: runs twenty threads against the target and writes the findings to results.txt; with -q the console stays quiet and only the file is written.

_No corpus evidence — derived from the packaged upstream source (options verified against the program's usage() output)._
