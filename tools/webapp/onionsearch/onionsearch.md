---
name: "onionsearch"
categories:
  - blackarch-webapp
version: "44.fc9d62c"
homepage: "https://github.com/megadose/OnionSearch"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-04"
build-system: python
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "megadose/OnionSearch"
github-stars: 1836
github-pushed: "2024-08-08"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# onionsearch

> Searches a word or phrase across 16 .onion search engines through Tor and merges the results into a CSV.

## What it's for

OnionSearch automates querying ".onion" search engines — ahmia, onionland, notevil, phobos, tordex, tor66, tormax, haystack, multivac, evosearch, deeplink and others — for a given string, then consolidates the results. It expects a Tor SOCKS proxy (default `127.0.0.1:9050`), fans queries out across engines in parallel, and writes a CSV of `engine, name, link` rows. Use it for OSINT over onion services; it is a search aggregator, not a crawler, and does not fetch the pages it finds.

## Typical use cases

### Search every supported engine

```
onionsearch "computer"
```

Outcome: fans the query out to all engines through the local Tor proxy and writes a CSV (default name `output_$SEARCH_$DATE.txt`) with one row per result.

### Query selected engines with a page limit

```
onionsearch "computer" --engines tor66 deeplink phobos --limit 3
```

Outcome: only the named engines are requested, loading at most 3 pages each — faster and lower-noise than the full sweep.

### Exclude engines from a full sweep

```
onionsearch "computer" --exclude ahmia candle
```

Outcome: every engine except the excluded ones is queried; useful when one engine is down or returns junk.

### Stream results to a file as they arrive

```
onionsearch "computer" --continuous_write True
```

Outcome: rows are appended to the output CSV as each engine responds instead of at the end, so partial results survive an interrupted run (`tail -f` on the file watches them land).

No corpus evidence — derived from upstream documentation.
