---
name: "gitgraber"
categories:
  - blackarch-recon
version: "82.aab4839"
homepage: "https://github.com/hisxo/gitGraber"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "hisxo/gitGraber"
github-stars: 2468
github-pushed: "2026-03-26"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gitgraber

> Python 3 tool that monitors GitHub in near real time for leaked credentials (AWS, Google, Stripe, Twilio, Mailgun, ...) tied to keywords you care about.

## What it's for

gitGraber watches GitHub's newly indexed content — not repository history — and applies regex signatures for tokens and API keys of many services (Google, AWS, PayPal, GitHub, Mailgun, Facebook, Twitter, Heroku, Stripe, Twilio, ...). You give it a keywords file and a query (typically a domain or product name); it scans the latest events, prints hits on the command line, and can notify Slack, Discord, or Telegram. It is designed for continuous monitoring of your organization's (or your vendors') exposure, so leaks from employees and service providers surface quickly. It deliberately trades recall for precision: regexes are tuned to keep false positives low, and already-reported repositories are remembered in `rawGitUrls.txt`. Before first use it needs GitHub tokens edited into `config.py`. It does not trawl full commit history — use a history-mining tool for that.

## Typical use cases

### Search for secrets matching a domain

Run a one-shot scan for a target domain, with Slack alerts.

```
gitgraber -k keywords.txt -q "target.example.com" -s
```

Outcome: queries GitHub for recently indexed content matching the keyword file plus the query, prints `POSSIBLE ... TOKEN FOUND` lines in the CLI for each regex hit, and posts matches to the Slack webhook configured in `config.py`.

### Build a fuzzing wordlist from discovered filenames

Harvest the filenames that appear in the matching results.

```
gitgraber -k keywords.txt -q "target.example.com" -w wordlist.txt
```

Outcome: dynamically fills `wordlist.txt` with filenames discovered on GitHub for the query, ready to feed to a fuzzer for content discovery against the same target.

### Monitor a query continuously

Turn the one-shot scan into a recurring watch.

```
gitgraber -k keywords.txt -q "target.example.com" -s -m
```

Outcome: creates a cron job that re-runs the search every 30 minutes and sends a Slack notification on new hits; already-scanned repositories listed in `rawGitUrls.txt` are skipped so alerts stay unique. Discord and Telegram are available via `-d` and `-tg`, and `-l 2` restricts results to commits less than N days old.

*No corpus evidence — derived from upstream documentation.*
