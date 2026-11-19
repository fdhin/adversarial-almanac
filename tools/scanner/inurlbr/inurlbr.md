---
name: "inurlbr"
categories:
  - blackarch-scanner
version: "34.dbf9773"
homepage: "https://code.google.com/p/inurlbr/"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-10"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "googleinurl/SCANNER-INURLBR"
github-match: "exact"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 879
github-pushed: "2021-04-09"
github-archived: false
github-issues: 2
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# inurlbr

> Googleinurl's INURLBR — a PHP dork engine that runs queries across ~25 search engines, then validates, filters and optionally exploits every URL found (GET/POST injection, email and URL harvesting).

*No corpus evidence — derived from upstream documentation (options read from the help banner in `inurlbr.php` of googleinurl/SCANNER-INURLBR at dbf9773, the commit BlackArch packages; that repository's README now points to MrCl0wnLab/SCANNER-INURLBR as the maintained continuation).*

## What it's for

inurlbr automates the search-engine side of web reconnaissance: you give it a dork (or a file of dorks), it pages through results on Google, Bing, Yandex, Shodan and roughly twenty other engines (rotating proxies or Tor if asked), and then — its distinguishing part — validates each candidate URL instead of just listing it. Validation types (`-t 1..5`) range from classic error-string matching through value-injection to source-file-driven path sweeps; `--exploit-get`/`--exploit-post` append payloads to each discovered target, and results can be filtered by HTTP code (`--ifcode`), deduplicated by domain (`--unique`), and pushed to IRC, email or file. It is an aggressive, request-heavy tool: run it only against targets you are authorized to test, and expect search-engine rate limiting. BlackArch installs the script as `inurlbr` (PHP with a shebang).

## Typical use cases

### Run a dork across all engines with deduplicated domains

```
inurlbr --dork 'inurl:item.php?id=' -q all --unique
```

Outcome: Queries every supported engine (`-q all`; individual or comma-separated engine numbers like `-q 1,2,5` also work) for the dork and prints the discovered URLs reduced to unique domains (`--unique`) — a first-pass target list for a specific parameter pattern.

### Validate results against a target's error signature

```
inurlbr --dork 'inurl:news.php?id=' -t 2 -a 'mysql_fetch'
```

Outcome: For every URL the dork finds, type-2 validation requests the page and keeps only those whose body contains the given string (`-a`, e.g. a database error fingerprint) — output is limited to URLs that actually exhibit the vulnerable behavior.

### Batch a dork file and filter by response code

```
inurlbr --dork-file dorks.txt -q 5 --ifcode 200
```

Outcome: Loads one dork per line from the file, runs them on engine 5, and reports only URLs answering HTTP 200 (`--ifcode`) — dead links and redirects drop out of the harvest. Add `--proxy localhost:8118` or `--tor-random` to distribute the querying.
