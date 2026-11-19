---
name: "zonedb"
categories:
  - recon
version: "1.0.3170-0parrot2"
homepage: "https://github.com/zonedb/zonedb"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: go
meta-checked: "2026-11-09"
github-repo: "zonedb/zonedb"
github-stars: 269
github-pushed: "2026-11-06"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 414
gh-activity-checked: "2026-11-09"
---

# zonedb

> The Public Zone Database: an exhaustive, nightly-updated list of public DNS zones with JSON metadata, queryable through the `zonedb` CLI.

## What it's for

ZoneDB is primarily a dataset: every public DNS zone (TLDs plus notable second/third-level zones like `co.uk`), including retired and withdrawn ones, in a sorted `zones.txt` plus one JSON metadata file per zone (tags, languages, RDAP/whois servers, wildcards). It overlaps with the Public Suffix List but aims to be exhaustive, is updated automatically each night by a CI workflow, and doubles as a Go library for programs that must reason about what constitutes a registrable domain. The packaged `zonedb` CLI queries and edits that dataset locally — filtering zones by tag or property and rewriting the files when curating — so it serves recon/OSINT tooling that needs authoritative zone lists rather than one-off lookups. The data is ODbL-licensed; the Go code MIT.

Commands below are verified against the upstream README.

## Typical use cases

### List all zones with wildcarded DNS

Find TLDs where any name resolves — useful when validating enumeration findings.

```
zonedb -list-wildcards
```

Outcome: prints every zone flagged in its metadata as wildcarded, one per line, straight from the local database.

### Filter zones by tag

Slice the database by how zones are categorized.

```
zonedb -tags brand
```

Outcome: lists all zones tagged `brand` (new gTLDs operated for brands); swap in `geo` or any other tag to filter differently, e.g. `zonedb -tags geo`.

### Inspect one zone's metadata

See what is recorded about a specific zone.

```
zonedb -zones capetown -list-tags
```

Outcome: prints the tags assigned to the `capetown` zone; the underlying record is the JSON file for that zone in the database's metadata directory.

### Enumerate IDN TLDs

Work with internationalized top-level domains specifically.

```
zonedb -tlds -idn -tags geo
```

Outcome: restricts output to top-level domains only (`-tlds`), keeps those with IDN labels (`-idn`) and filters by tag, printing the matching zone list — combinable with `-grep <regex>` for arbitrary content matches across metadata.
