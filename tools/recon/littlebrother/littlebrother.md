---
name: "littlebrother"
categories:
  - blackarch-recon
version: "112.338cf82"
homepage: "https://github.com/lulz3xploit/LittleBrother"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "lulz3xploit/LittleBrother"
github-stars: 931
github-pushed: "2021-02-20"
github-archived: true
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# littlebrother

> Menu-driven OSINT tool for the French-speaking countries — phone numbers, names, emails and social footprints of people in France, Belgium, Switzerland and Luxembourg, with no API keys required.

## What it's for

LittleBrother (by lulz3xploit) gathers open-source information about a person located in France, Belgium, Switzerland or Luxembourg through a set of search modules: directory and reverse-lookup sites, social networks, email and username lookups, and EXIF metadata from photos. It needs no API key and no account — every module scrapes public sources directly — and runs as a plain Python 3 script with an interactive menu. The project is archived in read-only state since 2021 (its authors moved on to a successor), so expect individual scrapers to break silently as the sites they parse change layout. People-search tooling has legal limits in most jurisdictions; use it on yourself or with authorization.

## Typical use cases

### Run an interactive search session

```
littlebrother
```

Outcome: starts LittleBrother's interactive menu (the package wraps `LittleBrother.py` under `/usr/share/littlebrother`); choose a module and enter the target's name, phone number or handle to get a structured summary of what the public sources returned for that person.

*No corpus evidence — derived from upstream documentation (lulz3xploit/LittleBrother README).*
