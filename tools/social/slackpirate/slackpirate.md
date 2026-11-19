---
name: "slackpirate"
categories:
  - blackarch-social
version: "0.20.r6.g99e470f"
homepage: "https://github.com/emtunc/SlackPirate"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "emtunc/SlackPirate"
github-stars: 777
github-pushed: "2025-01-31"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# slackpirate

> Given a Slack workspace token (or session cookie), enumerates and extracts the sensitive material scattered across it — credentials, keys, S3 links, interesting files.

## What it's for

SlackPirate audits what a low-privileged Slack member can actually reach: it walks the workspace via Slack's native APIs and hunts for AWS access keys, passwords, private keys, S3 bucket references, pinned messages across all channels, registration domains stale enough to take over, and downloadable files whose names or contents look sensitive. Red teams use it after landing any workspace token to find pivot material; blue teams use the same output to prove what should never have been posted. It needs both an API token (starts with `xoxc-`) and the browser `d` session cookie for full access, and it never sends messages or modifies the workspace — it is read/enumerate only.

## Typical use cases

### Validate a token and audit a workspace

```
python3 SlackPirate.py --token xoxc-xxxxxxxxxxxx --cookie d=xxxxxxxxxxxx
```

Outcome: checks token validity, reports admin/owner privileges if present, dumps the workspace user list (and access logs for privileged tokens) as JSON, then prints every finding category (S3 references, credentials, AWS keys, private keys, pinned messages, interesting URLs) and downloads keyword-matching files for offline review.

### Pivot from a stolen session cookie to workspace tokens

```
python3 SlackPirate.py --cookie d=xxxxxxxxxxxx
```

Outcome: lists the workspaces reachable with that cookie and the API tokens found in each — the enumeration step when you start from a browser session rather than a token.

### Run a single scan module or exclude one

```
python3 SlackPirate.py --token xoxc-xxxxxxxxxxxx --no-s3-scan
```

Outcome: all scans run except the S3 sweep (`--s3-scan` conversely runs only that module); `--interactive` swaps flags for a console UI that prompts for token/cookie and lets you tick scans, and `--verbose` adds per-request detail.

*No corpus evidence — derived from upstream documentation.*
