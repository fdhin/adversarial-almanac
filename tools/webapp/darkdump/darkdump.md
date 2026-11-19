---
name: "darkdump"
categories:
  - blackarch-webapp
version: "Darkdump.4.r5.g59912f6"
homepage: "https://github.com/josh0xA/darkdump"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-06"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "josh0xA/darkdump"
github-stars: 1815
github-pushed: "2026-04-08"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 11
gh-activity-checked: "2026-11-09"
---

# darkdump

> OSINT search interface that queries dark-web search engines and optionally scrapes each result.

## What it's for

Darkdump takes a search query, fetches results from one of six dark-web engines (ahmia, notevil, tordex, tor66, onionland, excavator), and can deep-scrape every result site for emails, metadata, keywords, documents and images. All results are filtered against Ahmia's public abuse blacklist regardless of engine; the uncensored engines ask for confirmation before running. Engines other than ahmia route through Tor, which must be running (control port configured in torrc) for `-p` modes. Breach-intelligence modes add credential-leak lookups. On BlackArch the `darkdump` command runs the CLI; upstream docs name the same entry point `darkdump-cli` and reserve plain `darkdump` for the tool's local web interface.

## Typical use cases

### Search without Tor via Ahmia

```
darkdump -q "privacy tools" -a 10
```

Outcome: fetches ten Ahmia results for the query and prints each with title, URL and description — no Tor needed because Ahmia is clearnet-indexed.

### Deep-scrape results over Tor

```
darkdump -q "marketplaces" -a 15 -s -p -i
```

Outcome: routes through the Tor proxy, scrapes every result site for metadata, links, emails and documents, and also collects images from each page.

### Switch engine, deduplicate, and export

```
darkdump -q "security research" -a 20 -e notevil -p -u -o results.json
```

Outcome: queries the notevil engine via Tor, hides duplicates that share title and description, and writes the result set to results.json — the file's extension (json/csv/txt) picks the format.

### Check a target against breach sources

```
darkdump --breach -q user@example.com -e ahmia -p
```

Outcome: runs the breach/credential-leak intelligence scan for the given address instead of a site search; `--breach-deep` adds scraping of each result.

_No corpus evidence — derived from upstream documentation (CLI flags verified against the packaged source's argparse)._
