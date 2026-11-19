---
name: "h8mail"
categories:
  - blackarch-recon
version: "344.ee31c8f"
homepage: "https://github.com/khast3x/h8mail"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "khast3x/h8mail"
github-stars: 5370
github-pushed: "2023-08-15"
github-archived: false
github-issues: 40
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# h8mail

> Email OSINT and breach-hunting front end: one query, every breach source.

## What it's for

h8mail takes email addresses — a single target, a bulk file, addresses
regex-matched out of other tools' output, or ones scraped from URLs — and
queries breach and intelligence services for each: HaveIBeenPwned,
Hunter.io, Snusbase, Leak-Lookup, Dehashed, IntelX, EmailRep and more,
regrouping the results per target into CSV or JSON. Beyond email addresses
it runs custom queries by username, IP, hash or domain against the keyed
APIs, and `--chase` automatically adds related addresses found during the
search to the target list. It also searches local breach dumps — the
Breach Compilation tree (`-bc`), cleartext files (`-lb`) and compressed
Collection-style archives (`-gz`) — with multiprocessing. API keys live in
a config file (`-c`) or on the command line (`-k "service=key"`). It is a
lookup and aggregation tool only; it never touches the target accounts.

## Typical use cases

### Query a single address across all services

```
h8mail -t user@example.com -c config.ini -o results.csv
```

Outcome: one row per target in results.csv summarizing every configured
breach/paste service that knows the address; use `-j` for JSON output
instead.

### Bulk-hunt a target list and chase related emails

```
h8mail -t targets.txt -c config.ini --chase 10 -o pwned_targets.csv
```

Outcome: every address in the file is queried, up to 10 related emails per
target (from Hunter.io, or all services with `--power-chase`) are added to
the ongoing search, and the combined results are regrouped in the CSV.

### Search local breach dumps without any API

```
h8mail -t targets.txt -bc /data/breach-compilation/ -sk
```

Outcome: greps the local Breach Compilation folder (or `-lb` cleartext and
`-gz` compressed dumps) for the targets using parallel workers and skips the
online services entirely (`-sk`); hits are regrouped in the output.

### Query by username or another identifier

```
h8mail -t user -q username -k "hunterio=KEY"
```

Outcome: performs a loose username query across services that support it;
the same pattern works with `-q ip`, `-q hash` or `-q domain` for non-email
pivots, with keys passed on the command line.

*No corpus evidence — derived from upstream documentation.*
