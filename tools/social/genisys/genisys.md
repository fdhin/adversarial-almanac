---
name: "genisys"
categories:
  - blackarch-social
version: "53.d53bb0c"
homepage: "https://github.com/ahayder/Genisys"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ahayder/Genisys"
github-stars: 3
github-pushed: "2021-11-28"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# genisys

> Telegram group-membership manipulation toolkit — scrape members from public groups and bulk-add them to another group using a pool of stored accounts.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Three Python scripts that together move an audience between Telegram groups: `manager.py` stores and filters the pool of Telegram accounts used for adding, `scraper.py` pulls active members from any public group into CSV files, and `tsadder.py` distributes those CSVs across the account pool, joins the destination group from every account and bulk-adds members (on the order of 50–60 per run), preferring user IDs over usernames because usernames are slower and miss users without one. It filters banned or limited accounts out of the pool automatically and can run many accounts in parallel. The author states the project is no longer maintained, and Telegram's anti-spam limits make account restrictions an expected occurrence — treat it as a snapshot of pre-limits tooling rather than dependable infrastructure. Account setup and workflow details live in the project's HOW_TO_USE.txt.

## Typical use cases

### Load accounts into the pool

Register the Telegram accounts that later runs will use.

```
python manager.py
```

Outcome: opens the account manager — add accounts, which are stored for all later scraping and adding runs; banned accounts are filtered out of the pool automatically.

### Scrape a public group's active members

Collect the member list to add elsewhere.

```
python scraper.py
```

Outcome: extracts active members of the chosen public group into CSV files, ready for distribution across the account pool.

### Bulk-add scraped members

Move the scraped audience into the destination group.

```
python tsadder.py
```

Outcome: every account in the pool joins the destination group and starts adding its share of the scraped members (by ID or username), reporting limited or failed accounts as it goes.
