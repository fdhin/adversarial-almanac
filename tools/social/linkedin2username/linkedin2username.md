---
name: "linkedin2username"
categories:
  - blackarch-social
version: "0.29.r4.g8889f30"
homepage: "https://github.com/initstring/linkedin2username"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "initstring/linkedin2username"
github-stars: 1895
github-pushed: "2026-05-20"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
---

# linkedin2username

> Generate probable corporate username and email lists from a company's LinkedIn employees.

## What it's for

linkedin2username is a pure web scraper (no API key) that logs into LinkedIn with your own credentials via a Selenium-driven browser, enumerates the employees of a target company, and turns each name into every common username format — writing separate wordlists (`first.last.txt`, `f.last.txt`, `flast.txt`, `firstl.txt`, `first.txt`, `lastf.txt`, `rawnames.txt`, plus a `metadata.txt` CSV of name/occupation), optionally with a domain appended for email spraying. Use it to build username/e-mail candidate lists before password audits; results depend on your connection depth (third-degree works), LinkedIn caps a single search at 1,000 employees, and the tool offers keyword- and geography-split searches to work around it. Rate limiting of the account you use is a real risk.

## Typical use cases

### Enumerate a company's employees

```
python3 linkedin2username.py -c targetco
```

Outcome: opens a browser for the LinkedIn login, scrapes the employee profiles behind `linkedin.com/company/targetco`, and writes all eight output files into `li2u-output/`.

### Produce email-address lists for spraying

```
python3 linkedin2username.py -c targetco -d 10 -n targetco.com -o li2u-targetco
```

Outcome: same enumeration bounded to 10 pages of 25 results, with `@targetco.com` appended to every candidate username, written to `li2u-targetco/`.

### Beat the 1,000-result search cap

```
python3 linkedin2username.py -c targetco -k 'sales,human resources,information technology'
```

Outcome: runs one enumeration per keyword, each under the cap, and their union exceeds what a single search returns; `--geoblast` achieves the same by splitting the search across geographic regions.

*No corpus evidence — derived from upstream documentation.*
