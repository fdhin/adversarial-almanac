---
name: "spfmap"
categories:
  - blackarch-recon
version: "8.a42d15a"
homepage: "https://github.com/BishopFox/spfmap"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-27"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BishopFox/spfmap"
github-stars: 40
github-pushed: "2015-04-01"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spfmap

> Mass SPF/DMARC surveyor that resolves email-authentication records for hundreds of domains into SQLite and reports how many are spoofable.

## What it's for

Measuring email-spoofing exposure across a domain portfolio. Feed it a file of domains (or a single target); worker goroutines resolve each domain's SPF and DMARC records and store the raw strings plus the parsed `all` mechanism and `p=` policy in an SQLite database, after which a report prints counts and percentages: domains with SPF and with DMARC, hard-fail `-all` policies, `p=reject`/`p=quarantine` DMARC policies, and the number of domains not readily spoofable. It is pure DNS — it never sends probe mail. The code is 2015-era Go with no upstream maintenance, but record lookups of this kind still work.

## Typical use cases

### Survey a list of domains

```
sqlite3 spfmap.db < db_setup.sql
spfmap -all -infile domains.txt -workers 10
```

Outcome: every domain's SPF and DMARC records are resolved (10 parallel workers) into `spfmap.db`, then the adoption and spoofability report prints. The first line creates the results table once per fresh database; `db_setup.sql` ships with the source.

### Check a single domain

```
spfmap -all -target example.com
```

Outcome: resolves and stores both record types for the one domain and then prints the report over the whole database.

### Re-generate the report later

```
spfmap -db spfmap.db
```

Outcome: with no scan flag (`-spf`/`-dmarc`/`-all`) given, no lookups are performed and only the summary report over the existing database is printed — report generation is on by default and `-db` selects the database file (`spfmap.db` by default).

*No corpus evidence — derived from upstream source (BishopFox/spfmap `spfmap.go` flag definitions and `db_setup.sql`; the README is a single line).*
