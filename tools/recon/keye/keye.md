---
name: "keye"
categories:
  - blackarch-recon
version: "29.d44a578"
homepage: "https://github.com/clirimemini/Keye"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "clirimemini/Keye"
github-stars: 100
github-pushed: "2019-12-30"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# keye

> URL change monitor with SQLite memory: it re-fetches your watched pages on a schedule and pushes a Slack notification whenever a response's body length differs from the stored baseline.

## What it's for

Watching target web pages for updates — new attack surface, content changes, defacements — without re-checking them by hand. Add single URLs or a list once; each run records every response's body length in a local SQLite database, compares against the stored value, and pushes the diffs to a Slack channel via an incoming webhook (configured in `slackconfig.py`). Designed to be scheduled (cron is the documented pattern, e.g. twice daily) on a small VPS. Detection is body-length-based only — same-length edits go unnoticed — and the Slack webhook is the sole notification channel. The upstream repository is archived; unmaintained since 2019.

## Typical use cases

### Start watching pages

```
python keye.py -s http://target.example.com
python keye.py -ul urls.txt
```

Outcome: `-s` adds a single URL to the monitored set, `-ul` imports a whole file of them (one URL per line); the first run records each page's baseline body length in the SQLite database. Subsequent scheduled runs compare against that baseline. `python keye.py -d` lists everything currently monitored, and `-rm http://target.example.com` removes an entry.

### Schedule the monitoring loop

```
0 */12 * * * cd /opt/Keye/ && /usr/bin/python /opt/Keye/keye.py
```

Outcome: a crontab entry (the upstream's documented deployment) that runs Keye every 12 hours; each run re-fetches all monitored URLs and, for any whose body length changed, updates the database and posts a Slack notification to the webhook's channel — the change alert lands without anyone watching the site.

*No corpus evidence — derived from upstream documentation.*
