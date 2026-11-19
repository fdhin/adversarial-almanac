---
name: "cmsscan"
categories:
  - blackarch-webapp
version: "43.f060b4b"
homepage: "https://github.com/ajinabraham/CMSScan"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ajinabraham/CMSScan"
github-stars: 1085
github-pushed: "2021-05-18"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cmsscan

> Self-hosted web dashboard that schedules and runs WPScan, Droopescan, JoomScan and VBScan for you.

## What it's for

CMSScan is a Flask web application that wraps four established CMS scanners (wpscan for WordPress, droopescan for Drupal, joomscan for Joomla, vbscan for vBulletin) behind one dashboard: you register target sites, kick off scans on demand, watch results in the UI, and can schedule recurring scans that email reports via SMTP. The scanners do the actual work — CMSScan's value is centralizing them for continuous monitoring of a portfolio of sites. It has no CLI of its own; the packaged launcher starts the web server. Authentication is off by default, so bind it to localhost or enable the built-in basic auth before exposing it.

## Typical use cases

### Start the scan dashboard

Bring up the web UI locally.

```
cmsscan
```

Outcome: launches the dashboard (gunicorn) listening on `127.0.0.1:7070`; browse there to add WordPress/Drupal/Joomla/vBulletin targets, run on-demand scans, and read the report pages.

### Run recurring scans with email reports

Automate the scheduled mode.

```
python3 /usr/share/cmsscan/scheduler.py
```

Outcome: after filling in the SMTP settings, recipient and per-CMS site lists at the top of `scheduler.py`, each run scans every configured site and emails the reports; wire it to a cron entry (for example `@weekly`) to make it recurring.

No corpus evidence — derived from upstream documentation.
