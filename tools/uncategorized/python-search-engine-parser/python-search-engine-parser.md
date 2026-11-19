---
name: "python-search-engine-parser"
categories:
version: "0.6.8"
homepage: "https://pypi.org/project/search-engine-parser/#files"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# python-search-engine-parser

> Python library plus `pysearch` CLI that queries popular search engines and scrapes result titles, links and descriptions.

## What it's for

search-engine-parser (PyPI) scrapes the result pages of Google, Bing, DuckDuckGo, Yahoo, Yandex, StackOverflow, GitHub, Baidu and other engines, returning structured results per engine (ranked titles, links, descriptions). It is an OSINT building block for automating and cross-referencing queries from scripts instead of a browser. The BlackArch `python-search-engine-parser` package installs the library and the `pysearch` console command. It is a scraper, not an official API client: engines change their markup over time and aggressive use triggers bot detection, so prefer the built-in proxy options for anything sustained.

## Typical use cases

### Query an engine from the command line

```
pysearch --engine bing --type descriptions "example.com security advisory"
```

Outcome: queries Bing and prints the description field of each result; `--type titles` or `--type links` select other fields, `-p 2` moves to result page 2, and `--proxy` routes the request.

### Scrape results from Python

```
python3 -c "
from search_engine_parser.core.engines.google import Search
results = Search().search('example.com security advisory', 1)
print(results['titles'][0])
print(results['links'][0])"
```

Outcome: uses the library API directly — `search(query, page)` returns a result object indexable by rank (`results[0]`) or by field name (`results['titles']`, `results['links']`, `results['descriptions']`), the building block for scripted cross-engine collection.

*No corpus evidence — derived from upstream documentation.*
