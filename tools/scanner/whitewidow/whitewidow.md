---
name: "whitewidow"
categories:
  - blackarch-scanner
version: "605.4f27bfe"
homepage: "https://github.com/Ekultek/whitewidow"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL3, custom:unknown
meta-checked: "2026-11-09"
github-repo: "Ekultek/whitewidow"
github-stars: 978
github-pushed: "2018-01-11"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# whitewidow

> Ruby SQL-vulnerability scanner that harvests its own targets from Google and can hand findings straight to sqlmap.

## What it's for

Whitewidow (Ekultek, 2016–2018, archived) automates the front half of SQL-injection hunting: it can scrape Google with one of 1,000+ built-in dork queries to build a target list, append multiple SQLi syntaxes to each URL, test the responses, and then launch sqlmap against confirmed vulnerable sites without leaving the program. It also runs from a plain file of URLs, spiders a single page for links to test, and randomizes user agents. The author abandoned it in favor of zeus-scanner, and its Google-scraping depends on the search landscape of its era — expect blocking and captchas today. It is a learning tool with sharp edges, not a maintained scanner; the sqlmap hand-off is the feature that made it popular.

## Typical use cases

### Run a default Google-dork sweep

```
ruby whitewidow.rb -d
```

Outcome: picks a random built-in search query, scrapes Google for candidate sites, appends SQL syntax to each and tests them; the banner, search progress and any vulnerable-site findings print to the console.

### Test a prepared list of URLs

```
ruby whitewidow.rb -f targets.txt
```

Outcome: reads the file line by line, adds its SQL injection syntaxes to each URL and reports which ones respond like they are injectable — the controlled alternative to the Google sweep.

### Hand confirmed findings to sqlmap

```
ruby whitewidow.rb -d --sqlmap
```

Outcome: after finding SQL-vulnerable sites, launches sqlmap using the run's `SQL_VULN.LOG` as the bulk target file and prompts for the sqlmap options you want, so triage goes straight to exploitation.

### Blend in and rehearse safely

```
ruby whitewidow.rb -f targets.txt --rand-agent --dry-run --batch
```

Outcome: uses a random user-agent header instead of the default one, `--dry-run` runs the program logic and prompts without actually attacking the sites it finds, and `--batch` keeps it from pausing for input — a safe first run to see what it would target.

*No corpus evidence — derived from upstream documentation.*
