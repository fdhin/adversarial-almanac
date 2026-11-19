---
name: "machinae"
categories:
  - blackarch-recon
version: "200.d5deae8"
homepage: "https://github.com/HurricaneLabs/machinae"
packaged-by: [blackarch]
blackarch-builddate: "2026-06-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "HurricaneLabs/machinae"
github-stars: 535
github-pushed: "2026-06-03"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# machinae

> Batch OSINT collector in the Automater tradition: hand it IPs, domains, URLs, hashes or email addresses and it queries dozens of reputation and intelligence sites about them in one pass.

## What it's for

Machinae (Hurricane Labs) collects intelligence about "observables" — IPv4/IPv6 addresses, FQDNs, URLs, email addresses, file hashes, SSL fingerprints. For each target it auto-detects the type and queries every configured service that applies: IPVoid, URLVoid, Fortinet categories, StopForumSpam, Cymru MHR, ProjectHoneypot, SANS, RIR whois, plus API-key services (VirusTotal, DomainTools, GreyNoise, IBM XForce, Metadefender) that need credentials in the config. Sources are defined in `/etc/machinae.yml`, merged with an optional per-user `~/.machinae.yml` or a file passed via `-c`; output is normal text or JSON (`-o J`). It is a collector and consolidator, not an analyzer — it reports what those sites know and nothing more, and queries against keyless API sources silently skip without keys.

## Typical use cases

### Look up an IP across reputation feeds

```
machinae 198.51.100.10
```

Outcome: auto-detects the target as an IPv4 observable, queries every enabled service for that type (verbose status lines per source by default; `-q` silences them) and prints one consolidated report section per service.

### Harvest intelligence on several targets into JSON

```
machinae -o J -f report.json 198.51.100.10 example.com https://example.com
```

Outcome: each target is auto-typed and queried, and the combined report is written as JSON to report.json instead of stdout (`-f -` would keep stdout) — ready to feed a downstream dashboard or ticketing pipeline.

### Restrict which sites run

```
machinae -s ipvoid,fortinet 198.51.100.10
```

Outcome: only the named services (comma-separated top-level keys from the machinae.yml config) are queried; the special keyword `all` runs every configured source including those marked `default: false`.

*No corpus evidence — derived from upstream documentation (HurricaneLabs/machinae README).*
