---
name: "spyse"
categories:
  - blackarch-recon
version: "47.cd11ba9"
homepage: "https://github.com/zeropwn/spyse.py"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "zeropwn/spyse.py"
github-stars: 270
github-pushed: "2020-08-18"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spyse

> Python API wrapper and command-line client for the Spyse internet-assets search service (spyse.com) — the service is dead, so this note documents the interface as it was.

## What it's for

spyse.py exposed the Spyse data-as-a-service APIs (FindSubdomains, CertDB, DNStable, PortMap, DomainsDB) through one client: subdomain enumeration, reverse-IP domain lookup, SSL-certificate search, per-record-type DNS dumps and Shodan-style search queries such as `"org: Microsoft"`, with paging behind a free API key and `--raw` for pipeable JSON. Spyse has since ceased operating — spyse.com and its API no longer resolve, and downstream integrations (e.g. theHarvester) have removed their Spyse modules — so the commands below fail at connection time today. They document how the client was used; for live equivalents use the Censys or Shodan clients.

## Typical use cases

### Enumerate subdomains (historical)

```
spyse -target example.com --subdomains
```

Outcome: printed every subdomain Spyse had observed for the domain; today the same command fails to resolve or reach the API host.

### Certificate and reverse-IP search (historical)

```
spyse -target example.com --ssl-certificates
spyse -target 198.51.100.10 --domains-on-ip
```

Outcome: listed SSL certificates associated with the domain, and the domains hosted on the IP, respectively.

### Raw JSON for pipelines (historical)

```
spyse -target example.com --dns-all --raw | jq
```

Outcome: emitted the API's raw JSON — here every DNS record type — for downstream processing with `jq` or aquatone; `-param domain` overrode input-type detection, and `-apikey`/`-page` enabled authenticated paging.

*No corpus evidence — derived from upstream documentation (zeropwn/spyse.py README); service shutdown verified by DNS resolution failure for spyse.com and by third-party removals of Spyse integrations.*
