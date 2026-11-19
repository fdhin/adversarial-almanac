---
name: "tookie-osint"
categories:
  - recon
version: "git (2026-10-26)"
homepage: "https://github.com/alfredredbird/tookie-osint"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "alfredredbird/tookie-osint"
github-stars: 3166
github-pushed: "2026-10-26"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 99
gh-activity-checked: "2026-11-09"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 6
---

# tookie-osint

> Username discovery across many websites — a Sherlock-style account finder with threading, Tor routing, CSV/JSON export and an optional web scraper.

## What it's for

Tookie-OSINT takes a username (or a list of them) and checks whether accounts with that name exist across a large catalog of websites, reporting the URLs where the account was found. It was rewritten from scratch in V4 for performance, is optimized for Python 3.12, and is packaged in Kali (`apt install tookie-osint`). Beyond simple existence checks it can route all traffic through Tor, run headless/scriptable, drive a selenium webscraper against discovered pages to harvest additional data, push notifications to a webhook, and write txt/csv/json results. It does not attempt logins or password guessing — it is purely account-presence enumeration.

## Typical use cases

### Check a single username everywhere

Enumerate a handle across the supported site catalog.

```
python3 brib.py -u user
```

Outcome: threaded checks (default 2 threads) run across the site list and a report of found account URLs is written in txt format (the default `-o txt`); the on-screen UI shows progress and positives.

### Check a whole username list

Process many handles in one run, script-friendly.

```
python3 brib.py -U usernames.txt -t 8 -sC -o csv
```

Outcome: each username from the file (one per line) is checked with 8 threads; `-sC` disables the interactive UI so only results are displayed, and `-o csv` writes the findings as CSV for import into a report.

### Route enumeration through Tor

Hide the source of the checks.

```
python3 brib.py -u user -tr
```

Outcome: all traffic is routed through Tor (requires a local Tor client); results are otherwise identical, at the cost of slower checks.

### Scrape data off the discovered accounts

Go beyond presence and collect page content.

```
python3 brib.py -u user -W -H -D 5
```

Outcome: `-W` enables the selenium webscraper on discovered profiles, `-H` harvests data from the sites it visits, and `-D 5` sets the seconds to wait for each page to load before extraction; scraped material lands in the results output instead of bare URLs.

*No corpus evidence — derived from upstream documentation.*
