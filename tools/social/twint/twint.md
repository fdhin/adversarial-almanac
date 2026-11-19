---
name: "twint"
categories:
  - blackarch-social
version: "845.e7c8a0c7"
homepage: "https://github.com/twintproject/twint"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "twintproject/twint"
github-stars: 16410
github-pushed: "2023-02-23"
github-archived: true
github-issues: 589
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# twint

> Historical: scraped Twitter timelines, followers and search results without the API — killed for good by X's 2023 shutdown of anonymous access.

## What it's for

Twint (twintproject) was an advanced Twitter scraping and OSINT tool that queried Twitter's web frontend directly — no API keys, no login, no rate limits — and could pull a user's tweets and timeline, followers/following lists, favorites, or search results with full search-operator, date and geo filtering, streaming tweets into files, CSV/JSON, SQLite or Elasticsearch, or driving the same queries from Python as a module. It stopped working when X locked down anonymous/guest frontend access in mid-2023, the repository was archived in February 2023, and no fix is possible without the paid API it existed to avoid — the tool is dead and documented here as historical reference. For current X/Twitter research the practical successors are the paid official API and community scrapers that keep chasing X's moving frontend (each with its own shelf life). The BlackArch package still installs the last upstream snapshot, and its Python-3.6-era dependencies (aiodns, cchardet, py-googletransx) are increasingly hard to satisfy on modern Python.

## Typical use cases

All examples below describe the documented interface as it worked before the 2023 shutdown — they now fail at the first request against x.com.

### Scrape a user's tweets

```
twint -u user
```

Outcome: printed the target's tweet stream to the terminal, including replies but excluding retweets; `--timeline` gathered ~3200 tweets including retweets, `--retweets` the last ~900 quickly.

### Harvest a social graph

```
twint -u user --followers --user-full
```

Outcome: listed every follower of the account, with `--user-full` fetching full profile records (bio, counts) per user; `--following` did the same for accounts the target follows.

### Search with operators and date bounds

```
twint -s "login example.com" --since 2023-01-01 --email --phone
```

Outcome: collected every public tweet matching the search string from the given date, and flagged those containing email addresses or phone numbers — the classic OSINT pivot for leaked contact data; `--verified` restricted results to verified accounts, `-g "48.880048,2.385939,1km"` to tweets geotagged inside a radius.

### Export for later analysis

```
twint -u user -o tweets.csv --csv
```

Outcome: wrote the scrape to CSV; `--json` with a `.json` output produced JSON, `--database tweets.db` poured results into SQLite, and `-es localhost:9200` indexed them into Elasticsearch.

*No corpus evidence — derived from upstream documentation.*
