---
name: "chaos-client"
categories:
  - blackarch-recon
version: "286.b0522e0"
homepage: "https://github.com/projectdiscovery/chaos-client"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-25"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/chaos-client"
github-stars: 892
github-pushed: "2026-11-05"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# chaos-client

> CLI for ProjectDiscovery's Chaos dataset: pull known subdomains for any domain from their API.

## What it's for

`chaos` is the official Go client for the Chaos dataset API — ProjectDiscovery's continuously updated index of subdomains harvested from internet-wide enumeration. A free API key from cloud.projectdiscovery.io authorizes queries (pass it with `-key` or export `PDCP_API_KEY`); the API is rate-limited to 60 requests per minute per IP and accepts domain names only. Output is newline-delimited subdomains, so results pipe cleanly into follow-on HTTP probing. It is a querying client, not an active enumerator — it returns what Chaos already knows.

## Typical use cases

### Enumerate subdomains for a domain

Fetch everything the dataset holds for one target.

```
chaos -d example.com -silent
```

Outcome: prints every subdomain Chaos has recorded for example.com, one per line, ready to pipe into further tooling.

### Check dataset coverage first

Size up a target before committing to a full pull.

```
chaos -d example.com -count
```

Outcome: prints only the statistics for the domain — how many subdomains the dataset currently holds.

### Batch query a domain list

Work through many scopes in one run.

```
chaos -dL domains.txt -o chaos-subs.txt
```

Outcome: queries each domain in the file and writes all results to the output file; add `-json` for machine-readable records per domain.

*No corpus evidence — derived from upstream documentation.*
