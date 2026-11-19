---
name: "raven"
categories:
  - blackarch-social
version: "33.8646a58"
homepage: "https://github.com/0x09AL/raven"
packaged-by: [blackarch, aur]
blackarch-builddate: "2021-04-27"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "0x09AL/raven"
github-stars: 799
github-pushed: "2020-05-18"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# raven

> A Linkedin information gathering tool that can be used by pentesters to gather information about an organization employees using Linkedin.

## What it's for

Raven is a Go tool for employee enumeration against LinkedIn during an
authorized engagement. Given a company name, it searches Google for
LinkedIn employee matches, stores what it finds in a local SQLite
database as a "scan", and from each stored scan can generate employee
email addresses in many common corporate formats (first.last, f.last,
first_last, ...) for a chosen domain — then optionally check every
generated address against Have I Been Pwned. It drives a real Chrome
session via chromedriver using the LinkedIn credentials you place in its
`config.conf`. Scans persist, so one crawl can be re-exported in every
email format without re-running. The upstream is unmaintained (last push
2020), and LinkedIn's anti-scraping makes success rate vary.

## Typical use cases

### Create and run a scan against a company

Collect employees of one organization into the database.

```
raven
new scan
set scan_name acme
set company ACME
set domain www
set pages_number 5
start
```

Outcome: After loading the LinkedIn credentials from config.conf, the
scan scrapes the configured number of Google result pages for LinkedIn
employee matches and stores every found employee in the local database
under the scan name `acme`.

### Export employee emails from a finished scan

Turn a stored scan into a target-domain address list.

```
raven
use acme
set domain example.com
set format first.last
export
```

Outcome: Generates one email per stored employee in the chosen format at
the chosen domain and writes them to the configured output file; `set
format ALL` produces every supported format at once.

### Check exported addresses against breaches

Add breach exposure to the email list.

```
raven
use acme
set domain example.com
checkpwned
```

Outcome: Queries Have I Been Pwned for each generated address and reports
which employees' corporate addresses appear in known breaches.

*No corpus evidence — derived from upstream documentation.*
