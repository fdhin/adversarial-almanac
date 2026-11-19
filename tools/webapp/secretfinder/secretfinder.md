---
name: "secretfinder"
categories:
  - blackarch-webapp
version: "15.d06119d"
homepage: "https://github.com/m4ll0k/SecretFinder"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "m4ll0k/SecretFinder"
github-stars: 2541
github-pushed: "2024-05-26"
github-archived: false
github-issues: 48
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# secretfinder

> Python script that mines JavaScript files for secrets — API keys, access tokens, JWTs, authorizations.

## What it's for

SecretFinder (by m4ll0k, built on the same approach as his LinkFinder
endpoint miner) takes a JavaScript file, a URL, a local file or a folder,
beautifies the code and runs a set of regular expressions tuned for
credential material: API keys, access tokens, JWTs and authorization strings.
Point it at a site and it can extract every JS link on the page and process
them all; results land in an HTML report or plain CLI output. It supports
Burp exported files, custom regexes for project-specific key formats,
cookies and headers for authenticated scripts, and proxying. Use it as the
standard first pass over a target's JS surface; note the namesake
SecretFinder by GerbenJavado (the LinkFinder author) is a different repo
with the same goal, and m4ll0k's rewrite is the one packaged here.

## Typical use cases

### Scan a single JavaScript file

Point it at one script and get an HTML report.

```
secretfinder -i https://target.example.com/app.js -o results.html
```

Outcome: downloads and beautifies the script, then writes results.html with
every regex hit — each candidate key/token with its surrounding context for
triage.

### Fast CLI output while hunting live

Skip the HTML report and jsbeautifier for speed.

```
secretfinder -i https://target.example.com/app.js -o cli
```

Outcome: prints matches straight to the terminal (noticeably faster since
the beautifier is bypassed) — convenient for quick interactive checking
during recon.

### Crawl a whole domain's scripts

Extract and process every JS link found on a page.

```
secretfinder -i https://target.example.com/ -e -g 'jquery;bootstrap;cdn.example.com'
```

Outcome: enumerates JavaScript links from the page, skips the noise
libraries named via `-g`, and reports secrets found across the remaining
scripts; add `-c 'session=...'` to reach authenticated scripts.

### Hunt a project-specific key format

Bring your own regex for non-standard tokens.

```
secretfinder -i https://target.example.com/app.js -o cli -r 'apikey=my.api.key[a-zA-Z]+'
```

Outcome: filters findings through your regex, printing only strings matching
your service's key format — the way to catch in-house token schemes the
built-in patterns do not know.

*No corpus evidence — derived from upstream documentation (m4ll0k/SecretFinder README help and usage examples; installed as `/usr/bin/secretfinder` wrapping SecretFinder.py).*
