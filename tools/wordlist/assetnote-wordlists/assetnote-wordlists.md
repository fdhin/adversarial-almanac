---
name: "assetnote-wordlists"
categories:
  - blackarch-wordlist
version: "183.6dea231"
homepage: "https://github.com/assetnote/wordlists"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-01"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "assetnote/wordlists"
github-stars: 1753
github-pushed: "2026-02-27"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# assetnote-wordlists

> Assetnote's monthly-generated wordlists for content and subdomain discovery, built from HTTP Archive crawl data.

## What it's for

A pure dataset with no CLI. Assetnote regenerates these lists around the 28th of each month with Commonspeak2 from HTTP Archive data and publishes them at wordlists.assetnote.io; the BlackArch package snapshots the four dataset families under `/usr/share/assetnote-wordlists/`:

- `automated/` — dated snapshots like `httparchive_directories_1m_<YYYY>_<MM>_<DD>.txt`, `httparchive_subdomains_<date>.txt` and `httparchive_apiroutes_<date>.txt`: real paths and hostnames observed on internet-facing hosts, ranked by crawl frequency.
- `technologies/` — per-technology file and extension lists (`httparchive_<technology>_<date>.txt`).
- `manual/` — stable-named curated lists (`phpmillion.txt`, `2m-subdomains.txt`, `best-dns-wordlist.txt`, plus mirrored `raft-*` lists).
- `kiterunner/` — precompiled route archives (`routes-small.kite.tar.gz`, `routes-large.kite.tar.gz`, `swagger-wordlist.txt`) for kiterunner-style API content discovery.

Because `automated/` and `technologies/` files carry their generation date in the filename, reference the newest snapshot present on your install when scripting. Compared with SecLists, these lists favor paths currently deployed on real hosts over curated classics, so they complement rather than replace it.

## Typical use cases

### Survey the installed datasets

```
ls /usr/share/assetnote-wordlists/
ls /usr/share/assetnote-wordlists/automated/ | tail
```

Outcome: prints the four dataset families and the newest dated snapshot of each generated list, giving you the exact filenames to reference in the commands that follow.

### Directory brute-force with live-crawl data

```
ffuf -w /usr/share/assetnote-wordlists/automated/httparchive_directories_1m_2026_08_27.txt \
     -u http://target.example.com/FUZZ -ic -t 100
```

Outcome: ffuf fuzzes the URL path with the million most-crawled directories from HTTP Archive data (auto-calibration strips wildcard responses); the hits are paths actually seen on internet-facing hosts, which surfaces staging and API paths that generic lists miss.

### Subdomain enumeration from observed hostnames

```
ffuf -w /usr/share/assetnote-wordlists/manual/2m-subdomains.txt \
     -u http://target.example.com -H 'Host: FUZZ.target.example.com' -fs 42
```

Outcome: vhost-style enumeration using a two-million-entry hostname corpus; hostnames whose response differs from the baseline size are printed as live subdomains to resolve and probe.

*No corpus evidence — derived from upstream documentation.*
