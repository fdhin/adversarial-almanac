---
name: "gggooglescan"
categories:
  - blackarch-scanner
version: "0.4"
homepage: "http://www.morningstarsecurity.com/research/gggooglescan"
packaged-by: [blackarch]
blackarch-builddate: "2024-09-05"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Seabreg/gggooglescan"
github-stars: 1
github-pushed: "2019-03-18"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gggooglescan

> Automated Google querying with anti-bot countermeasures: turns one search term into thousands of harvested URLs or hostnames.

## What it's for

GGGooglescan (v0.4, 2011, by Andrew Horton aka urbanadventurer, Security-Assessment.com; original home morningstarsecurity.com, now mirrored on GitHub) is a bash script that performs automated Google searches and returns the results as URLs or bare hostnames. Automated Google scraping normally dies fast — Google throttles and captchas obvious bot behavior — so the tool's core value is pacing and technique: it stops before Google's "omitted some entries" wall, detects the `sorry.google.com` captcha redirect and waits an hour before retrying, rotates user agents, and implements horizontal searching (combining the query with every word of a wordlist for many shallow queries instead of deep-paging one query). The classic product is recon lists: hosts running a specific software (`"powered by <forum>"`), or URL sets for further tooling. It needs `curl` and `perl`, and by Google's rules this is ToS-violating automation — expect IP-level blocks after heavy use, which is what the sleep (`-s`) and proxy-pass (`-x`) options mitigate. Binary: `gggooglescan`.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Scrape results for a query into a log

Basic run, appending to a log file:

```
gggooglescan -l results.log '"powered by mybb"'
```

Outcome: pages through the Google results for the quoted query (default depth), writing every result URL to `results.log` (appended if the file exists), stopping before the "omitted some entries" wall. If Google serves the captcha page instead, the script detects it and pauses for 60 minutes before retrying rather than burning the IP.

### Harvest at scale with horizontal searching

Combine the query with a wordlist to get many shallow queries:

```
gggooglescan -l horizontal.log -d 1 -e wordlist '"powered by mybb"'
```

Outcome: `-e` combines the query with each word of the wordlist in turn (query `a`, query `aachen`, ...) and `-d 1` takes only the first results page of each — the horizontal pattern that yielded thousands of results where deep-paging one query gets blocked after a few hundred. A bundled `wordlist` ships in the package; any one-word-per-line file works.

### Return hostnames instead of URLs

When the next tool wants bare hosts:

```
gggooglescan -o -l hosts.log '"powered by mybb"'
```

Outcome: `-o` strips each result to its hostname, producing a deduplicated target-host list ready for port scanning or DNS resolution, instead of full result URLs.

### Pace and proxy the scraping

To stretch a run past Google's bot detection:

```
gggooglescan -s 2 -l slow.log -d 1 -e wordlist '"powered by mybb"'
gggooglescan -x "--socks5 127.0.0.1:9050" -l proxied.log '"powered by mybb"'
```

Outcome: `-s SECONDS` sleeps between each query (README runs with `-s 2` and reports over 100,000 results before stopping, versus fast runs blocked after ~60 wordlist words), and `-x` passes arbitrary arguments to curl — the example routing the scraping through a SOCKS proxy so the source IP rotates independently of the tool. Add `-c au` (or `uk`, `nz`) to bias results to one country's Google front-end.
