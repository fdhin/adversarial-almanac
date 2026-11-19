---
name: "domain-stats"
categories:
  - blackarch-recon
version: "169.759c52c"
homepage: "https://github.com/MarkBaggett/domain_stats"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "MarkBaggett/domain_stats"
github-stars: 231
github-pushed: "2023-11-09"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# domain-stats

> Local web API by Mark Baggett that ages domains: registration dates, first-contact tracking and anomaly alerts served as JSON.

## What it's for

domain-stats runs a small local HTTP service that, queried with a domain name, answers with the domain's registration ("born-on") date via RDAP, whether it is NEW or ESTABLISHED relative to your threshold, the date your sensors first saw it, and letter-frequency scores that flag algorithmically generated names. The point is alerting: point a SIEM, Zeek or LogStash at it and get alerts such as YOUR-FIRST-CONTACT or SUSPECT-FREQ-SCORE when something never-seen and freshly registered shows up in DNS or proxy logs. It is a server you stand up next to your sensors — not an enumeration or record-query tool.

## Typical use cases

### Initialise the data directory and start the server

```
domain-stats-settings /opt/domain-stats-data
domain-stats /opt/domain-stats-data
```

Outcome: the settings command creates or edits the configuration files in the given directory (pressing Enter accepts the defaults); the second command starts the API server, listening on port 5730 by default.

### Ask the server about a domain

```
wget -q -O- http://127.0.0.1:5730/example.com
```

Outcome: a JSON object with `alerts` (e.g. YOUR-FIRST-CONTACT), `category` (NEW or ESTABLISHED), `freq_score`, `seen_by_web` (registration date) and `seen_by_you` — the fields a SIEM rule or Zeek script keys on.

### Preload established domains and repair RDAP gaps

```
domain-stats-utils -i top1m.import -nx /opt/domain-stats-data
```

Outcome: imports roughly 35K top-1M domains tagged `top1m` so the server does not flood RDAP on first start and never alerts on common sites (`-nx` marks them never-expire); run `domain-stats-utils -f /opt/domain-stats-data` afterwards to backfill records RDAP could not resolve using whois.

### Check server health

```
wget -q -O- http://127.0.0.1:5730/stats
```

Outcome: prints cache and RDAP efficiency statistics and runs a database consistency check (the database is locked while it runs, so do not poll it constantly).

*No corpus evidence — derived from upstream documentation.*
