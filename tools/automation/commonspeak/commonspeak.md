---
name: "commonspeak"
categories:
  - blackarch-automation
version: "36.f0aad23"
homepage: "https://github.com/assetnote/commonspeak2"
packaged-by: [blackarch]
blackarch-builddate: "2019-07-04"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "assetnote/commonspeak2"
github-stars: 781
github-pushed: "2023-02-12"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# commonspeak

> Builds content-discovery and subdomain wordlists from Google BigQuery datasets of the real web.

## What it's for

Commonspeak2 (Assetnote) generates wordlists by querying publicly available Google BigQuery datasets — HTTP Archive's latest scans, HackerNews, and GitHub's public dataset — so the output reflects technologies and paths actually deployed on the web right now, rather than a static list's vintage. It produces subdomain lists, file paths filtered by extension, deleted-file names mined from public commit history, and framework route paths. Running it yourself requires a GCP service account with BigQuery access, and queries cost real money — the routes queries are explicitly expensive (over $20 per framework) and prompt for confirmation unless `--silent` is passed. If you only want the lists, Assetnote publishes the pre-generated monthly wordlists at wordlists.assetnote.io; the repo itself is dormant (last push 2023).

## Typical use cases

### Generate a subdomain wordlist

The default mode, sourced from HackerNews and HTTP Archive scans.

```
./commonspeak2 --project my-gcp-project --credentials key.json subdomains -o subdomains.txt
```

Outcome: runs the subdomain SQL against both BigQuery sources, logs rows extracted per source, and writes the deduplicated subdomain list to `subdomains.txt` for use with dnsx/massdns-style resolvers.

### Build a file-path wordlist for given extensions

Extension-filtered paths straight from GitHub's dataset.

```
./commonspeak2 --project my-gcp-project --credentials key.json ext-wordlist -e aspx,php,html,js -l 100000 -o web-paths.txt
```

Outcome: extracts up to 100000 (`-l`) paths ending in the requested extensions and writes them to `web-paths.txt`; `-e` accepts a single extension or a comma-separated set.

### Hunt deleted files in public repos

Files developers removed may still exist on forgotten infrastructure.

```
./commonspeak2 --project my-gcp-project --credentials key.json deleted-files -l 50000 -o deleted.txt
```

Outcome: mines GitHub's commits dataset for deleted file names and writes up to 50000 of them to `deleted.txt`, suitable for targeted content discovery.

### Extract framework route paths

Turn routing declarations into realistic request paths.

```
./commonspeak2 --project my-gcp-project --credentials key.json routes --frameworks rails -l 100000 -o rails-routes.txt
```

Outcome: pulls `config/routes.rb` declarations from GitHub, converts them into valid concrete paths (e.g. `/:id` becomes `/1234`), normalizes them, and saves to `rails-routes.txt`; this query is expensive (> $20 per framework) and prompts to confirm — pass `--silent` to skip the prompt.

*No corpus evidence — derived from upstream documentation.*
