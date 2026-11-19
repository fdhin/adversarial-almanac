---
name: "spiga"
categories:
  - blackarch-webapp
version: "690.f1831cd"
homepage: "https://github.com/getdual/scripts-n-tools/blob/master/spiga.py"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-01"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "getdual/scripts-n-tools"
github-stars: 88
github-pushed: "2026-02-25"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# spiga

> Configurable, threaded web resource scanner driven by a plain-text rules file.

## What it's for

spiga probes a web server for interesting resources (admin panels, backup files, SSH keys,
CMS fingerprints, webshells, and much more) based on a configuration file that defines what
to look for and how to judge a hit. Every check is user-editable: functions group resources,
and each function declares an action — match an HTTP status code (`;code=200`), dump the
response (`;dump`), or search a regex in the body (`;search=`). Resources support extension
expansion such as `admin.(asp|aspx|php)`. It is a scanner focused on resource/content
discovery, not a vulnerability exploiter. The shipped `spiga.conf` contains large ready-made
function sets (quick scan, admin panels, phpMyAdmin, WordPress, logs, SSH keys, shells).

## Typical use cases

### Quick scan with the default configuration

Run the shipped wordlist-driven checks against one target.

```
spiga http://target.example.com
```

Outcome: threaded requests for every resource in the default `spiga.conf` functions in the working directory; each resource matching its function's status/regex action is reported.

### Scan with a custom rules file

Point the scanner at your own configuration instead of the default `spiga.conf`.

```
spiga -c custom.conf https://target.example.com
```

Outcome: only the functions and resources from `custom.conf` are requested, letting you scope the scan to a specific technology or file class.

### Low-and-slow scan with request visibility

Add a random delay between requests and print every request as it is made.

```
spiga -s -r http://target.example.com
```

Outcome: each request line is shown (`-r`) and a random number of seconds is slept between requests (`-s`), trading speed for noise reduction.

No corpus evidence — derived from upstream documentation.
