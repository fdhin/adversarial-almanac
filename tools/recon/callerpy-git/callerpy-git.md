---
name: "callerpy-git"
categories:
  - recon
version: "20140518.r40"
homepage: "https://github.com/Logic-gate/callerpy"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Logic-gate/callerpy"
github-stars: 92
github-pushed: "2016-12-18"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# callerpy-git

> Legacy CLI that scrapes Truecaller's web flow to resolve a phone number to a subscriber name.

## What it's for

callerpy (2014, Python 2) performs reverse phone lookups by driving truecaller.com the way a browser would, authenticated through a social login (Twitter, Google+ or Facebook) whose credentials must be hardcoded in the script before use. Numbers can be qualified by country name or dialing code, and a crawler mode works through a `num.list` file with a configurable pause between requests to respect Truecaller's rate limit. Truecaller's interface and anti-bot measures have changed drastically since — treat this as a historical tool that is unlikely to work against the live site without rework.

## Typical use cases

### Look up a single number

Resolve one phone number to a name using the hardcoded social login.

```
callerpy -n 5550123456 -c us -l twitter
```

Outcome: queries Truecaller for the number qualified by country string and prints the matched subscriber as name/number/country fields.

### Look up by dialing code

Same lookup with the target qualified by country code instead of country name.

```
callerpy -n 5550123456 -cc 1 -l twitter
```

Outcome: resolves the number under dialing code 1; useful when the country string form is ambiguous or unsupported.

### Crawl a number list in bulk

Work through many numbers while respecting the service's rate limit.

```
callerpy -crawl 2 -l twitter
```

Outcome: reads entries formatted as `CC;number` (one per line) from num.list, sleeping 2 seconds between requests; running `callerpy` later with no arguments replays the accumulated lookup log.

*No corpus evidence — derived from upstream documentation.*
