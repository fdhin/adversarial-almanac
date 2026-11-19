---
name: "ankosint"
categories:
  - scanner
version: "git (2025-12-01)"
homepage: "https://github.com/dogucyber/ankosint"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "dogucyber/ankosint"
github-stars: 11
github-pushed: "2025-12-01"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# ankosint

> Menu-driven OSINT helper from DOGU Cyber Security that enriches phone numbers and hunts usernames across social platforms and people directories.

## What it's for

AnkOsint is an interactive, single-script Python tool: launching it opens a menu where you
pick a lookup module and supply a phone number, username or similar identifier. Behind the
menu it wraps the `phonenumbers` library (carrier, region, timezone, line type and
validity for a number), scrapes Google for traces of a query, checks identifiers against
major social platforms (facebook.com, instagram.com, x.com and youtube.com are among the
domains in its lookup table), and consults people-directory sites such as whitepages.com
and pagesjaunes.fr; a bundled `MAC_ADDRESS.txt` database supports hardware-vendor lookups.
It is an aggregator over public sites rather than a data owner, and its script ships with
obfuscated source, so review what it does before pointing it at real engagements. Use only
for authorized OSINT research.

## Typical use cases

### Launch the interactive lookup menu

Start the tool and work through its modules.

```
python3 AnkOsint.py
```

Outcome: after `pip install -r requirements.txt`, opens the menu-driven console; pick a
module (phone-number intelligence, username/social checks, directory search) and enter the
identifier to get the enrichment report printed to the terminal.

*No corpus evidence — derived from the upstream README and the packaged script's lookup table and imports.*
