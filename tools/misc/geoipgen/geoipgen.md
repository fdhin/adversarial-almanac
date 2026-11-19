---
name: "geoipgen"
categories:
  - blackarch-misc
version: "0.4"
homepage: "https://code.google.com/archive/p/geoipgen/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-14"
build-system: scripts
license: GPL-1.0-or-later
meta-checked: "2026-11-09"
github-repo: "Seabreg/geoipgen"
github-stars: 0
github-pushed: "2019-09-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# geoipgen

> Generates country-targeted IPv4 lists from the MaxMind GeoLite Country database.

## What it's for

A Ruby tool by Andrew Horton (MorningStar Security) that inverts the usual GeoIP lookup: instead of resolving an IP to a country, it takes one or more country codes and emits the IPv4 addresses allocated to them — in random or sorted order, each address once or with repeats, optionally capped at a count. It reads the MaxMind GeoLite Country database (`GeoIPCountryWhois.csv`, located with `-c` or from `~/.geoipgen/`); the first run for a country builds and caches that country's address map under `~/.geoipgen/` so subsequent runs start faster. Typical purpose: building geographically scoped target lists for scanning or measurement.

GitHub: third-party mirror of the Google Code upstream (code.google.com/p/geoipgen).

## Typical use cases

### List every address allocated to a country

```
geoipgen nz
```

Outcome: prints each IPv4 address allocated to New Zealand exactly once in random order; the first invocation additionally builds and saves the country's address map under `~/.geoipgen/`.

### Cap output across several countries

```
geoipgen -n 10000 cn hk jp
```

Outcome: emits addresses drawn from China, Hong Kong and Japan and exits once 10000 have been printed — a bounded sample for staged testing.

### Measure a country's allocation size

```
geoipgen --sorted il | wc -l
```

Outcome: streams Israel's allocated addresses in ascending order and the pipeline prints their count — the number of IPv4 addresses MaxMind attributes to that country.

### Generate an unbounded stream with repeats

```
geoipgen --repeat us
```

Outcome: continually emits random US addresses with repetitions allowed, until interrupted — useful for long-running randomized sampling.

*No corpus evidence — derived from upstream documentation.*
