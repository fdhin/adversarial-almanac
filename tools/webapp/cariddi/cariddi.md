---
name: "cariddi"
categories:
  - blackarch-webapp
version: "v1.4.6.r16.g39aebc1"
homepage: "https://github.com/edoardottt/cariddi"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "edoardottt/cariddi"
github-stars: 3805
github-pushed: "2026-11-05"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 60
gh-activity-checked: "2026-11-09"
---

# cariddi

> Crawl a list of domains and hunt for endpoints, secrets, keys and juicy files.

## What it's for

Cariddi takes URLs on stdin (one target or a whole file of them), crawls each site, and simultaneously hunts for: secrets and API keys (regex-based), juicy endpoints and parameters, errors, useful information disclosures, and interesting file extensions. It is the crawl-and-collect stage of a web assessment — it maps the attack surface rather than testing it. Written in Go, single binary, actively maintained; configuration covers concurrency, delay, proxying, custom headers and user-agents, ignore lists, and custom secret/endpoint definition files.

## Typical use cases

### Crawl a single target

Quick one-off crawl of one site.

```
echo https://target.example.com/ | cariddi
```

Outcome: crawls the site from the given URL and prints every discovered URL plus any error pages and info disclosures it encounters along the way; multiple targets work by piping a file instead (`cat urls.txt | cariddi`).

### Hunt for secrets and juicy endpoints

Turn the crawl into a key/token hunt.

```
cat urls.txt | cariddi -s -e
```

Outcome: `-s` matches crawled content against the built-in secret/token regexes (API keys, tokens, credentials) and `-e` flags juicy endpoints (admin, login, upload, backup-style paths); custom patterns can be loaded with `-sf secrets.txt` and `-ef endpoints.txt`.

### Find juicy files by extension

Target interesting file types at a chosen depth.

```
cat urls.txt | cariddi -ext 2
```

Outcome: reports discovered files with extension-interest level 2 of 7 (e.g. archives, backups, logs — tune the level to widen or narrow the net); `-ie pdf,png,jpg` excludes noisy extensions from any hunt.

### Crawl quietly through a proxy with rate control

Stay controlled and observable on sensitive targets.

```
cat urls.txt | cariddi -proxy http://127.0.0.1:8080 -d 2 -c 200 -rua
```

Outcome: routes all crawling through the given HTTP/SOCKS5 proxy (mirroring traffic into an intercepting proxy), waits 2 seconds between pages, caps concurrency at 200, and rotates a random browser user-agent per request; `-intensive` additionally expands the crawl to all discovered subdomains of the targets.

No corpus evidence — derived from upstream documentation.
