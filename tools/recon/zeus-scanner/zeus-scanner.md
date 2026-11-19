---
name: "zeus-scanner"
categories:
  - blackarch-recon
version: "414.21b8756"
homepage: "https://github.com/Ekultek/Zeus-Scanner"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Ekultek/Zeus-Scanner"
github-stars: 999
github-pushed: "2023-11-20"
github-archived: false
github-issues: 420
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# zeus-scanner

> Automated dork-scanning console: scrapes search engines via Selenium for dork results, then chains sqlmap, nmap, XSS and admin-panel checks against what it finds.

## What it's for

Zeus (Ekultek) wraps Google/Bing/DuckDuckGo/AOL dork searching in an automated browser (Selenium + Firefox) so queries look like real traffic, extracts result URLs — including unwrapping Google ban-URLs and webcache links to survive IP blocks — and can immediately run assessments on the harvest: sqlmap SQLi scans through the sqlmap API, nmap port scans, XSS checks with tamper scripts, admin-panel discovery, clickjacking tests and whois lookups. It can pull dorks one at a time (`-d`), from a file (`-l`), or at random from 5000+ bundled dorks (`-r`). It is a Python 2-era tool tied to Firefox 52–58 and geckodriver, so treat it as legacy: modern search-engine scraping and separate modern scanners usually serve better, and automated scanning of third-party sites requires authorization.

Commands below are verified against the upstream wiki (full flag reference).

## Typical use cases

### Run a single dork

Search one dork and collect the resulting sites.

```
python zeus.py -d "inurl:admin login.php"
```

Outcome: launches the automated browser against the default engine (Google), scrapes result URLs for the dork and prints them; add `-B`/`-D`/`-A` to switch to Bing, DuckDuckGo or AOL.

### Work through a dork list across multiple result pages

Batch-recon with a large dork file.

```
python zeus.py -l dorks.txt -M -L 100 --batch --random-agent
```

Outcome: runs every dork in `dorks.txt`, requesting multiple pages per dork (`-M`) up to 100 links each (`-L`), skipping interactive prompts (`--batch`) and rotating user-agents; dead dorks are auto-blacklisted to a file.

### Chain sqlmap onto dork results

Turn found URLs straight into SQLi assessment.

```
python zeus.py -d "inurl:item.php?id=1" -s --auto
```

Outcome: after scraping, connects to the sqlmap API server (`--auto` attempts to start it) and runs a sqlmap scan against every discovered URL carrying a query parameter; `--sqlmap-args 'dbms mysql, level 3'` forwards options to sqlmap.

### Assess found sites beyond injection

Layer additional quick checks onto the harvest.

```
python zeus.py -d "site:example.com" -a -x -w
```

Outcome: for the URLs found by the dork, hunts admin panels (`-a`, with `-P` to show every connection tried), probes for reflected XSS with optional `--tamper` payload obfuscation (`-x`), and performs whois lookups on the domains (`-w`); `-p` would add an nmap port scan of each host instead.
