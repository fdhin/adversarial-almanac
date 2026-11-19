---
name: "torcrawl"
categories:
  - blackarch-webapp
version: "v1.35.r4.g2fb5d78"
homepage: "https://github.com/MikeMeliz/TorCrawl.py"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-05"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "MikeMeliz/TorCrawl.py"
github-stars: 546
github-pushed: "2026-06-29"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 62
gh-activity-checked: "2026-11-09"
---

# torcrawl

> Crawl and extract regular or .onion webpages through the Tor network.

## What it's for

TorCrawl.py (MikeMeliz) is a Python script for anonymous web scraping via the Tor network.
With a single argument it fetches a page — `.onion` or regular — through Tor and prints its
source, so output pipes cleanly into other tools. Beyond single pages it crawls a site to a
settable depth, collecting internal links into a file, with optional pauses between
requests, random user-agent rotation, and export of results as JSON, XML, or into a SQLite
database with an HTML visualization. It can also run without Tor, using a custom SOCKS5
proxy or rotating a proxy list. A running Tor service (default SOCKS5 on 127.0.0.1:9050)
is assumed unless `-w` is used. Respect robots.txt and site terms — the tool will crawl
whatever it is pointed at.

## Typical use cases

### Fetch one page through Tor and pipe it on

Read a page's source anonymously and process it with standard tools.

```
torcrawl -u http://target.example.com/ | grep 'google-analytics'
```

Outcome: the page is fetched over Tor and its source goes to stdout, ready for grep or any downstream tool. Add `-e -o page.html` to write the extracted source to a file instead, or `-i urls.txt` to process a file of URLs.

### Crawl a site to a depth with pauses

Walk a site's links layer by layer, throttled.

```
torcrawl -v -u http://target.example.com -c -d 2 -p 2
```

Outcome: verbose output confirms Tor readiness and your exit IP, then the crawler follows links to depth 2 with a 2-second pause between requests; all discovered links land in `links.txt` (under a per-site output folder).

### Crawl without Tor through rotating proxies

Run the same crawl on clearnet infrastructure.

```
torcrawl -u http://target.example.com -c -w -rpr -rua
```

Outcome: `-w` bypasses Tor entirely, `-rpr` rotates through the proxy list in `res/proxies.txt` (one `host:port` per line), and `-rua` rotates user agents — useful for distributed, non-Tor crawling. Add `-j`/`-x` to export the crawl as JSON/XML, or `-DB` plus `-vis` for a SQLite link graph with an HTML visualization.

No corpus evidence — derived from upstream documentation.
