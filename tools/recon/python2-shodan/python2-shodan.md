---
name: "python2-shodan"
categories:
  - blackarch-recon
version: "1.28.0"
homepage: "https://github.com/achillean/shodan-python"
packaged-by: [blackarch, arch]
blackarch-builddate: "2022-07-17"
arch-builddate: "2026-03-06"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "achillean/shodan-python"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 3478
github-pushed: "2024-08-05"
github-archived: false
github-issues: 64
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# python2-shodan

> The official Shodan Python library and CLI, packaged for Python 2 — search, host lookups, counts, bulk downloads and the streaming firehose from one `shodan2` command.

## What it's for

This is the official `shodan` client library and command-line utility (by John Matherly, achillean), packaged for Python 2; BlackArch installs the CLI as `shodan2` to coexist with the Python 3 build. The library (`from shodan import Shodan`) wraps the REST, streaming and alerts APIs — search, per-IP banners, facet counts, bulk data downloads and real-time firehose consumption — and the CLI exposes the same surface as subcommands: `init`, `info`, `myip`, `search`, `host`, `count`, `download`, `parse`, `convert`, `domain`, `stats`, `stream`, `honeyscore`, `radar`, plus `alert`/`data`/`org`/`scan` groups. Everything needs an API key from account.shodan.io. Prefer the Python 3 `shodan` package for new work; this build exists for Python 2 tooling.

## Typical use cases

### Store your API key

One-time setup for every later command.

```
shodan2 init <api-key>
```

Outcome: validates the key against the API and saves it locally so all subsequent `shodan2` commands authenticate automatically.

### Look up a host

Full service banner inventory for one IP.

```
shodan2 host 198.51.100.10
```

Outcome: prints every open port/service Shodan has observed on the address, with banners, product/version strings and observation timestamps — a passive footprint of the host.

### Search and count with the query language

Find and size matched populations.

```
shodan2 search --fields ip_str,port,title 'apache'
shodan2 count 'apache country:"DE"'
```

Outcome: `search` prints one result per matching banner using only the requested fields; `count` returns the total number of matches for the query — the standard pair for measuring an exposure population before pulling data.

### Bulk download and parse offline

Work a large result set locally.

```
shodan2 download results.json.gz 'apache'
shodan2 parse --fields ip_str,port results.json.gz
```

Outcome: `download` fetches up to the page limit of banners into a compressed JSON.gz file; `parse` streams that file and prints selected fields — repeatable offline analysis without re-querying credits.

### Check your egress IP

Quick API connectivity test.

```
shodan2 myip
```

Outcome: prints the IP address Shodan sees you connecting from — a fast sanity check that the key and network path work.

### Use the library from Python 2 code

Embed Shodan in a Python 2 tool.

```
python2 -c "from shodan import Shodan; api = Shodan('<api-key>'); print(api.host('198.51.100.10')['ports'])"
```

Outcome: initializes the client and prints the observed port list for the IP from within Python 2 code; `api.search_cursor()` iterates full result pages and `api.count()` mirrors the CLI's count.

No corpus evidence — derived from upstream documentation.
