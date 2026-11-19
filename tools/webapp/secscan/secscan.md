---
name: "secscan"
categories:
  - blackarch-webapp
version: "1.5"
homepage: "https://code.google.com/archive/p/secscan-py/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-12"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "Seabreg/secscan.py"
github-stars: 0
github-pushed: "2019-01-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# secscan

> Menu-driven Python 2 web application scanner with dork-driven SQLi, LFI, XSS and RFI checks plus utility modules.

## What it's for

SecScan (v1.5, by m0le, released on Google Code around 2013) is an interactive, menu-driven
security Swiss-army script aimed at beginners. From a single numbered menu it runs search-engine
dork driven checks for SQL injection, LFI, XSS and RFI, locates admin pages, enumerates subdomains
and cracks MD5 hashes against dictionaries or online cracking services. It is a Python 2 era tool:
the packaged launcher still runs it, but expect dated output and no maintenance. It does not
produce structured reports — results are printed to the console and saved to per-check text files.

GitHub: third-party mirror of the upstream Google Code secscan-py project.

## Typical use cases

### Run the interactive scan menu

Start the tool and pick a module from the menu.

```
secscan
```

Outcome: the SecScan banner and a numbered menu appear (1 SQLi, 2 LFI, 3 XSS, 4 RFI, 5 proxy
check, 6 admin page finder, 7 subdomain scan, 8 dictionary MD5 cracker, 9 online MD5 cracker,
10 show your IP). Each dork-driven option (1-4) crawls candidate sites, prints which are
vulnerable, and appends findings to a per-check file in the working directory (`sqli.txt`,
`lfi.txt`, `xss.txt` or `rfi.txt`); the utility options prompt for their own inputs. Ctrl-C
aborts the current job and returns to the shell.

No corpus evidence — derived from the packaged upstream source (Google Code archive secscan.py v1.5).
