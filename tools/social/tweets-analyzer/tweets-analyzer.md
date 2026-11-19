---
name: "tweets-analyzer"
categories:
  - blackarch-social
version: "55.8d6bd3c"
homepage: "https://github.com/x0rz/tweets_analyzer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "x0rz/tweets_analyzer"
github-stars: 2980
github-pushed: "2023-11-14"
github-archived: false
github-issues: 38
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tweets-analyzer

> Profiles a Twitter account's timeline — activity rhythms, clients, timezone, hashtags and interaction networks — from its metadata.

## What it's for

Tweets Analyzer (x0rz) is a single-script profiler for OSINT triage of a Twitter account: feed it a screen name and it pulls the account's recent tweets and reports average activity by hour and weekday, the interface timezone and language, which clients (Android, web, bots) posted, geotagged tweets, most-used hashtags, most-retweeted and most-mentioned users, and — with `--friends` — a clustering of the account's friends by timezone and language to guess where an account really operates from. It authenticates with the official Twitter v1.1 REST API via keys you paste into `secrets.py`, which is also its practical limitation: since X ended free API access the required keys are only available on paid API tiers, so treat the tool as working but conditional on having API credentials. It analyzes public tweet metadata only — no login bypass, no protected accounts.

## Typical use cases

### Baseline a target account

```
tweets-analyzer -n user
```

Outcome: fetches up to 1000 recent tweets and prints the full profile — activity histograms by hour/day, timezone and language settings, source clients, hashtags, top retweeted/mentioned users — the first-pass read of who runs the account.

### Filter tweets by posting client

```
tweets-analyzer -n user -f android
```

Outcome: only tweets posted from the Android app are analyzed, separating genuine mobile activity from automated or cross-posted sources; swap the filter value for any client string seen in the unfiltered source breakdown.

### Cluster the account's friends by locale

```
tweets-analyzer -n user --friends --utc-offset 3600
```

Outcome: pulls each friend's declared timezone and language (rate-limited to 15 requests) and groups them, revealing the account's real linguistic/geographic orbit; `--utc-offset` shifts the activity histogram into a local timezone instead of UTC.

### Export the analysis

```
tweets-analyzer -n user -e report.txt --no-timezone
```

Outcome: writes the full report to `report.txt` (add `-j` for JSON) with the timezone auto-adjustment disabled, ready to attach to an investigation file.

*No corpus evidence — derived from upstream documentation.*
