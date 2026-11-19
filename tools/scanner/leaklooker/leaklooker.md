---
name: "leaklooker"
categories:
  - blackarch-scanner
version: "5.0d2b9fc"
homepage: "https://github.com/woj-ciech/LeakLooker"
packaged-by: [blackarch]
blackarch-builddate: "2019-02-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "woj-ciech/LeakLooker"
github-stars: 1472
github-pushed: "2020-06-28"
github-archived: true
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# leaklooker

> LeakLooker: queries BinaryEdge for internet-exposed databases and services and prints what each leaks.

## What it's for

LeakLooker (woj-ciech, archived) turns the BinaryEdge attack-surface API into a
one-command leak finder: select the exposed-service classes you care about —
Elasticsearch, CouchDB, MongoDB, GitLab, Rsync, Jenkins, SonarQube, Kibana,
Cassandra, RethinkDB, open directory listings, and Amazon S3 buckets (per region) —
and it walks the result pages, printing for every hit what an unauthenticated
visitor would see: database names and tables, GitLab/Jenkins instance details,
directory contents. Results can be narrowed with a free-form BinaryEdge query
(`--query`, e.g. a country filter or keyword), so a run can be scoped to an
organization or region. It needs a BinaryEdge subscription API key, pasted into the
script itself before use, and iterates result pages between `--first` and `--last`.
A later GUI variant (LeakLooker-X) exists; this note covers the packaged console
tool. Use it for OSINT-style exposure monitoring of your own ranges.

## Typical use cases

### Find open database services

```
leaklooker --mongodb --elastic --first 1 --last 10
```

Outcome: Walks BinaryEdge result pages 1-10 for exposed MongoDB and Elasticsearch instances, printing per host the exposed endpoint plus readable contents (databases, collections/indices) — each hit an unauthenticated exposure to triage.

### Scope a search by country or keyword

```
leaklooker --jenkins --gitlab --first 1 --last 2 --query "country:UY"
```

Outcome: Applies the BinaryEdge query as a filter, so only Jenkins and GitLab instances in the given country (or matching any keyword/query expression) are fetched, with per-instance details such as registration-open status on GitLab and job visibility on Jenkins.

### Enumerate exposed S3 buckets per region

```
leaklooker --s3usa --first 1 --last 10
```

Outcome: Lists Amazon S3 buckets found exposed in the selected region (also `--s3europe`, `--s3asia`) that permit listing — bucket names and their readable contents, the starting point for open-bucket triage.

*No corpus evidence — derived from upstream documentation.*
