---
name: "redditosint"
categories:
  - recon
version: "git (2026-10-04)"
homepage: "https://github.com/albertrg99/redditosint"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "albertrg99/redditosint"
github-stars: 40
github-pushed: "2026-10-04"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# redditosint

> Username-driven Reddit OSINT: pulls a user's full post/comment history from public mirrors and reports activity patterns, exposed identifiers and an exposure score.

## What it's for

`redditosint` (script `reddit_osint.py`) profiles a Reddit account by username. It harvests indexed posts and comments — including deleted content recovered from archive mirrors (Arctic Shift, PullPush) — and produces a report with activity patterns, subreddits frequented, items that look like exposed identifiers, and an overall exposure score. Output is JSON to stdout (summary) or to a file (full per-item dump), with filters for subreddit, keywords, NSFW and pagination caps. Because Reddit and the mirrors rate-limit aggressively, it ships with round-robin proxy rotation, ban cooldowns and page-level exponential backoff; it is a read-only OSINT collector and does not interact with accounts.

## Typical use cases

### Quick profile summary

```
python3 reddit_osint.py user
```

Outcome: prints a JSON summary of the account's activity (subreddits, timing patterns, flagged identifiers, exposure score) to stdout, with progress logs on stderr — the fast first look at a username.

### Save the complete activity dump

```
python3 reddit_osint.py user --json report.json --quiet > /dev/null
```

Outcome: writes `report.json` containing the `all_items` key — every post and comment with text, subreddit, score, date, live/deleted status and permalink — while keeping the terminal quiet; the on-screen summary deliberately omits this bulk.

### Narrow a search

```
python3 reddit_osint.py user --subreddit netsec --keywords "employer" --max-pages 10
```

Outcome: only indexes content from the named subreddit (or mentioning the keyword), capped at 10 pages of 100 items — targeted triage instead of a full archive pull; `--over18 true` restricts to NSFW content, `--source arctic` uses a single mirror for speed.

### Collect through rotating proxies

```
python3 reddit_osint.py user --proxies proxies.txt --proxy-stats
```

Outcome: requests are spread round-robin over the proxies in `proxies.txt` (http/https/socks5, one per line); blocked proxies go on a 30-second cooldown (`--cooldown`) while the run continues through the others, and `--proxy-stats` adds per-proxy ok/banned/error counts to the report.

*No corpus evidence — derived from upstream documentation.*
