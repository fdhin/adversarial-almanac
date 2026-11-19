---
name: "grabber"
categories:
  - blackarch-webapp
version: "0.1"
homepage: "http://rgaucher.info/beta/grabber/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "neuroo/grabber"
github-stars: 148
github-pushed: "2016-09-14"
github-archived: true
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# grabber

> Early Python 2 web application scanner with black-box and source-aware (hybrid) analysis modes.

## What it's for

Grabber (Romain Gaucher, v0.1 era) is an early web application vulnerability scanner: it spiders a site and tests for SQL injection, blind SQL injection, XSS, file insertion, backup files, session handling and JavaScript code issues, optionally pairing black-box probing with source-code analysis by handing fetched sources to an external analyzer (PHP-SAT by default; any analyzer plugs in via XML attack patterns). Its own README calls it an artifact of the past ("use Burp or w3af"), the upstream is archived, and it targets the Python 2/PHP-era web — reference material rather than a modern go-to. During hybrid analysis it copies sources to `./local/current` and writes analysis output under `./local/analyzed`.

## Typical use cases

### Scan a site for the classic injection classes

```
python2 grabber.py -u http://target.example.com -s -b -x
```

Outcome: spiders the target and reports SQL injection, blind SQL injection and XSS candidates per parameter to the console, flagging spots to confirm manually.

### Spider deeply and hunt backup files and file insertion

```
python2 grabber.py -u http://target.example.com -d 2 -z -i
```

Outcome: enumerates files to depth 2, probes for backup copies of everything discovered, and runs file-insertion patterns — with fetched sources kept under `./local/current` for the hybrid pass.

No corpus evidence — derived from upstream documentation and the grabber.py option parser.
