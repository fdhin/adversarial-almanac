---
name: "goohak"
categories:
  - blackarch-recon
version: "31.815a31e"
homepage: "https://github.com/1N3/Goohak"
packaged-by: [blackarch]
blackarch-builddate: "2021-10-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "1N3/Goohak"
github-stars: 741
github-pushed: "2024-03-05"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# goohak

> Fire a battery of Google-dork queries at a domain straight from the terminal.

## What it's for

GooHak is a small bash script that automates Google hacking (dorking) against
a single domain: pass it a hostname and it opens a browser tab for each query
in its built-in list — login/admin pages, indexed directories, backup files,
documents and other classic dorks scoped to the target — turning an hour of
manual dork crafting into one command. Reach for it in the reconnaissance
phase when you want a quick sweep of everything Google has indexed about a
domain you are authorized to assess. It does not scrape or parse results
programmatically: queries launch in your browser via xdg-open, so it is an
interactive enumeration aid, subject to the same CAPTCHAs as a manual Google
session, not a headless scanner.

## Typical use cases

### Launch the dork battery at a domain

One command, one browser, every query in the script's list.

```
goohak target.example.com
```

Outcome: opens one browser tab per dork scoped to the domain (admin/login
pages, exposed backups, indexed documents); review the tabs for anything
sensitive Google has surfaced, then pivot the interesting URLs into deeper
enumeration.

*No corpus evidence — derived from upstream documentation.*
