---
name: "scavenger"
categories:
  - blackarch-recon
version: "114.17fe369"
homepage: "https://github.com/rndinfosecguy/Scavenger"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "rndinfosecguy/Scavenger"
github-stars: 875
github-pushed: "2026-11-04"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 20
gh-activity-checked: "2026-11-09"
---

# scavenger

> OSINT bot that crawls paste sites in real time looking for leaked credentials.

## What it's for

Scavenger is a Python bot that watches paste sites — pastebin.com (public archive scraping plus a user-tracking mode), pastes.io, GitHub Gists, dpaste.com and pastecode.io — and saves any paste containing email:password pairs or other sensitive strings. Detection is driven by a customizable search-terms file plus built-in regexes covering private keys, cloud API token prefixes (sk_live_, AIza, github_pat_), and database/message-broker connection URIs. Modules run as background tmux sessions; crawled pastes are filed under data/raw_pastes, credential hits under data/files_with_passwords and other sensitive data under data/otherSensitivePastes, and a bundled web dashboard summarizes the take. The pastes.io module needs an API key in configs/pastesio.json.

## Typical use cases

### Scrape the pastebin.com archive

Continuously process newly published pastes for leaks.

```
python3 scavenger.py -0
```

Outcome: starts the archive-scraping module in a background tmux session; matching pastes accumulate under data/files_with_passwords and data/otherSensitivePastes while non-matching ones are kept in data/raw_pastes.

### Scrape GitHub Gists alongside pastebin

Combine any set of modules in one run.

```
python3 scavenger.py -0 -3
```

Outcome: both the pastebin archive scraper and the GitHub Gist scraper run as tmux sessions, widening coverage to gist leaks.

### Track specific pastebin users

Monitor accounts known to publish leaks (listed in configs/users.txt).

```
python3 scavenger.py -1
```

Outcome: pastes published by the tracked users are captured as they appear, rather than waiting for them to surface in the archive.

### Review findings on the dashboard

```
python3 scavenger.py -8
```

Outcome: starts the local web dashboard summarizing the collected leaks.

*No corpus evidence — derived from upstream documentation.*
