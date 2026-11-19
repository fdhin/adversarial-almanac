---
name: "doork"
categories:
  - blackarch-webapp
version: "6.90c7260"
homepage: "https://github.com/AeonDave/doork"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "AeonDave/doork"
github-stars: 137
github-pushed: "2016-07-26"
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

# doork

> Passive Vulnerability Auditor.

## What it's for

doork is a passive vulnerability auditor: it runs Google dorks (from the Google Hacking Database or a custom wordlist) against a target domain and collects indexed pages that may expose flaws — all without ever contacting the target itself. Reach for it during the passive recon phase when you want search-engine-indexed exposure (leaked files, admin panels, error pages) before touching the endpoint. It does no active scanning, no crawling and no exploitation, and it is frozen 2016-era Python 2 code (the upstream repository is archived) — search engines also gate automated queries aggressively, so expect captchas and treat results as a hint list, not a scan. The BlackArch package wraps the script as `/usr/bin/doork`.

## Typical use cases

### Audit a domain passively with the built-in dorks

Point the bundled GHDB-derived dork list at a domain.

```
doork -t example.com -o doork.log
```

Outcome: runs every dork in its database against the target on Google, prints matches to the console, and appends a full log of findings to `doork.log` (`-o`) — a passive list of indexed, potentially interesting URLs gathered without sending a single packet to the target.

### Scan with a custom dork wordlist

Use your own dork list, one dork per line.

```
doork -t example.com -w dorks.txt
```

Outcome: replaces the built-in database with `dorks.txt`, running each line as a Google query against the target — the standard way to focus the audit on a specific exposure class (documents, panels, backups).

### Refresh the dork database

Update the GHDB data before an audit.

```
doork -g
```

Outcome: downloads the latest Google Hacking Database entries into the local database directory and exits (`-u`/`--update` similarly pulls the latest program code from the repository).

No corpus evidence — derived from upstream documentation and source (getopt definitions and the built-in help text of `doork.py`).
