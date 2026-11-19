---
name: "belati"
categories:
  - blackarch-scanner
version: "72.49577a1"
homepage: "https://github.com/aancw/Belati"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "aancw/Belati"
github-stars: 568
github-pushed: "2021-06-10"
github-archived: true
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# belati

> Belati — an interactive OSINT collection framework in the spirit of Metasploit: set a domain or company and `start`, and it gathers whois, subdomains, emails, public documents and their metadata, employees and leaked mail into a SQLite-backed project with a Django web view.

*No corpus evidence — derived from upstream documentation (README plus the packaged `Belati.py` shell source).*

## What it's for

One session, one domain, everything public about it. Belati (Indonesian for "dagger"; the project styles itself "The Traditional Swiss Army Knife For OSINT") chains the classic domain-OSINT moves: whois (with Indonesian TLD support), subdomain enumeration, service scanning across the found subdomains, email harvesting from websites, search engines and the MIT PGP key server, public-document scraping with author/metadata (Exif) extraction, organization and employee lookup, and Git/SVN exposure and robots.txt checks on the subdomains. Findings land in a SQLite project database, and a Django web-management view (`startws`) serves them. It is a python2-era tool from 2017-2021 (upstream archived, unmaintained) — expect python2 dependencies and treat it as legacy. It collects public data only; it is not an attack tool.

## Typical use cases

### Open a collection session

```
belati
```

Outcome: drops into the interactive shell (`?` prints the command menu: set, show, start, startws, version, history, `!` for shell escape). All targeting happens through shell parameters, not command-line flags.

### Configure the target and run the collection chain

Inside the `belati` shell:

```
set domain example.com
set orgcomp "Example Inc"
show options
start
```

Outcome: `show options` confirms the six parameters (domain, orgcomp, email, username, proxy, proxy_file — domain or orgcomp is required), and `start` executes the collection chain — whois, subdomains, service scan, mail and document harvesting — creating a new numbered project, storing results in its SQLite database and printing per-stage progress. `startws` instead launches the Django web-management server alone; root is only needed for the nmap service-scan stage.
