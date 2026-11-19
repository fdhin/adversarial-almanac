---
name: "metafinder"
categories:
  - blackarch-recon
version: "v1.2.r2.g30c8475"
homepage: "https://github.com/Josue87/MetaFinder"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Josue87/MetaFinder"
github-stars: 223
github-pushed: "2024-01-19"
github-archived: true
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# metafinder

> Search-engine document hunter that mines a domain's public files for metadata.

## What it's for

MetaFinder (by Josué Encinar) searches Google, Bing and Baidu for documents hosted under a target domain, downloads what the engines expose, and extracts metadata (authors, software, emails, paths) into a report — the classic OSINT move of learning an organization's internal usernames and tooling from files it accidentally indexed. The engines throttle aggressively, so results depend on what is currently indexed; Google is the default engine, Baidu support is labeled experimental by the author. It is read-only reconnaissance and needs no API keys. The upstream repository is archived (2024), so the interface is frozen at v1.2. No corpus evidence — derived from upstream documentation.

## Typical use cases

### Full three-engine metadata sweep

Run every search engine against a domain.

```
metafinder -d example.com -l 20 -o metafinder-report -go -bi -ba
```

Outcome: searches Google (`-go`), Bing (`-bi`) and Baidu (`-ba`) for up to 20 results each (`-l`), downloads the documents found, and writes extracted metadata plus a report into the `metafinder-report` output directory.

### Google-only pass with more threads

Quick targeted run against the best-indexed engine.

```
metafinder -d example.com -l 100 -t 10 -o report
```

Outcome: Google-only search (the default engine when none is flagged) for up to 100 results using 10 worker threads (`-t`, default 4); the report directory accumulates discovered documents with their metadata — look first at author and software fields for internal username leaks.
