---
name: "snscrape"
categories:
  - blackarch-recon
version: "0.4.3.20220106"
homepage: "https://github.com/JustAnotherArchivist/snscrape"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "JustAnotherArchivist/snscrape"
github-stars: 5467
github-pushed: "2023-11-15"
github-archived: false
github-issues: 73
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snscrape

> Python command-line scraper for social networking services that turns user profiles, hashtags and searches into streams of item URLs or full JSON records.

## What it's for

Collecting posts and metadata from social platforms without official APIs: Twitter/X (users, hashtags, searches, single tweets and threads, lists, trends), Facebook (profiles, groups, communities), Instagram (profiles, hashtags, locations), Mastodon, Reddit (users, subreddits, searches), Telegram channels, VKontakte profiles and Weibo. The CLI's default output is one URL per discovered item; `--jsonl` emits the complete record (content, datetime, images, counts) for machine processing. The package also doubles as an importable Python library, though that usage is undocumented upstream. Development stopped in 2023 and later platform lockdowns — above all Twitter/X's API closure — have broken or degraded several scrapers, so treat any run as best-effort and check the project issues for the current state per platform.

## Typical use cases

### Collect everything an account posted

```
snscrape twitter-user user > tweets.txt
```

Outcome: prints the URL of every post by the account, newest first; redirect to a file for later processing.

### Take a bounded sample of a hashtag

```
snscrape --max-results 100 twitter-hashtag example
```

Outcome: stops after 100 results; each output line is the URL of one matching post.

### Emit full structured records

```
snscrape --jsonl --max-results 50 twitter-user user > tweets.jsonl
```

Outcome: one JSON object per line carrying all extracted fields (content, datetime, media, counts) — filter and aggregate downstream with `jq`.

### Discover the available scrapers

```
snscrape --help
```

Outcome: lists every scraper name the installed version supports plus the global options; each scraper documents its own arguments via `snscrape <scraper-name> --help`.

*No corpus evidence — derived from upstream documentation (JustAnotherArchivist/snscrape README).*
