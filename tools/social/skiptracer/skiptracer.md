---
name: "skiptracer"
categories:
  - blackarch-social
version: "123.ca40957"
homepage: "https://github.com/84KaliPleXon3/skiptracer"
packaged-by: [blackarch]
blackarch-builddate: "2020-12-04"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "84KaliPleXon3/skiptracer"
github-stars: 26
github-pushed: "2023-07-01"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# skiptracer

> Menu-driven OSINT scraper that pulls people-data (names, phones, emails, plates, breach hits) from public-records sites without any API keys.

## What it's for

Skiptracer (originally by xillwillx, packaged from the 84KaliPleXon3 tree) was built for passive reconnaissance on a budget: instead of paid APIs (Recon-NG transforms, Maltego), it BeautifulSoup-scrapes free people-search and paywall sites to compile PII on a target. Its plugin modules cover truepeoplesearch-style lookups, truthfinder and 411 pages, haveibeenpwned, caller-ID sites, license plates, LinkedIn, name-availability checkers and background-check previews; results can be dumped to txt or JSON. It is a Python 2 interactive tool — you walk its console menus, it is not scriptable — and scraping breaks whenever the underlying sites change markup, so verify each module's output before relying on it.

## Typical use cases

### Run the interactive lookup menu

```
python2 skiptracer.py
```

Outcome: prints the banner and main menu of scraper plugins; pick a module, enter the target's name/phone/email/plate when prompted, and the scraped records are printed to screen. Answering the output prompt with a filename (for example `out.json`) writes the collected data to disk in the chosen format.

### Run it containerised without a Python 2 host install

```
docker run --rm -it xshuden/skiptracer
```

Outcome: the same interactive menu inside a throwaway container — the practical option on modern hosts that no longer ship Python 2, with no trace left when the container exits.

*No corpus evidence — derived from upstream documentation.*
