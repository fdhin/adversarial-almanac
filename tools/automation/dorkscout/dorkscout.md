---
name: "dorkscout"
categories:
  - blackarch-automation
version: "1.0.r13.gdd87daf"
homepage: "https://github.com/R4yGM/dorkscout"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2022-02-20"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "R4yGM/dorkscout"
github-stars: 243
github-pushed: "2021-08-18"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dorkscout

> Run Google-dork lists (the Exploit-DB GHDB categories) against a target — or the whole internet — and collect the matching URLs.

## What it's for

dorkscout automates Google dorking: it downloads the Google Hacking Database category lists from Exploit-DB, runs each dork as a search, and reports the URLs that come back — exposed files, login portals, sensitive directories, vulnerable applications — either internet-wide or restricted to a specific site. Use it to sweep an organization's search-engine exposure with the standard dork corpora instead of pasting dorks by hand. Results land in a plain-text or HTML report and traffic can be routed through an HTTP/SOCKS proxy. It only queries the search engine: it never touches the hosts it finds, so pair it with your own validation.

## Typical use cases

### Install the dork lists

Fetch the GHDB categories the scanner consumes.

```
dorkscout install --output-dir ./dorks
```

Outcome: downloads every category as a `.dorkscout` file (Files Containing Passwords, Sensitive Directories, Pages Containing Login Portals, Various Online Devices, ...) into ./dorks and prints the list as it lands.

### Scan a specific site

Restrict a dork category to one organization.

```
dorkscout scan -d './dorks/Sensitive Directories.dorkscout' -t target.example.com -O results.txt
```

Outcome: every dork in the list run against target.example.com with matching URLs appended to `results.txt`; `-l` caps results per dork (default 100).

### Scan internet-wide through a proxy with an HTML report

Full-corpus sweep with traffic hidden behind a local proxy.

```
dorkscout scan -d './dorks/Files Containing Passwords.dorkscout' -H report.html -x socks5://127.0.0.1:9050
```

Outcome: a browsable HTML report of every URL the dork list found (no `-t` means no site restriction), with all search traffic routed through the local SOCKS5 proxy.

### Clean up downloaded lists

Remove stale dork files before a fresh install.

```
dorkscout delete -d ./dorks
```

Outcome: deletes all `.dorkscout` files inside the given directory, so the next `install` starts from a clean set.

*No corpus evidence — derived from upstream documentation.*
