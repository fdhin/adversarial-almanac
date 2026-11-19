---
name: "sn0int"
categories:
  - recon
homepage: "https://github.com/kpcyrd/sn0int"
origin: "awesome-pentest"
recipe: "upstream"
packaged-by: [arch, nixpkgs]
build-system: rust
github-repo: "kpcyrd/sn0int"
github-stars: 2564
github-match: "exact"
gh-checked: "2026-11-14"
github-commits-year: 2
gh-activity-checked: "2026-11-14"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-14"
license: GPLv3+
---

# sn0int

> Semi-automatic OSINT framework and package manager: enumerate a target's attack surface from public sources, map everything into one queryable database, chain modules on the results.

## What it's for

sn0int (kpcyrd, inspired by recon-ng and Maltego) gathers intelligence about a target — or about yourself — by semi-automatically processing public information and mapping the results in a unified format for followup investigations. Nothing is hardcoded: recon logic ships as sandboxed Lua modules installed from the sn0int registry (`sn0int.com`), so subdomain harvesting from CT logs and passive DNS, mass resolution with http/https service discovery, ASN/GeoIP enrichment, emails from PGP keyservers and whois, breach-compromised logins, cross-site profile discovery, passive-ARP local network enumeration and social-media activity harvesting are all pluggable modules you can extend or publish yourself. Discovered entities live in a per-workspace database with a query language (`select domains where ...`), and modules run against whatever the current `target` selection yields, which is what makes investigation chaining natural. It is a passive/aggressive OSINT collector — not an exploit framework.

## Typical use cases

### Bootstrap the workspace with default modules

```
sn0int
```

Outcome: on first run sn0int downloads its public suffix list and GeoLite2 databases, then suggests `pkg quickstart` — typing it installs the featured module set (ctlogs, dns-resolve, asn, geoip, hackertarget/otx subdomains, pgp-keyserver, passive-spider, threatminer) and lands you in the `[sn0int][default] >` console.

### Scope a target and enumerate subdomains

```
add domain
use ctlogs
run
```

Outcome: `add domain` prompts for the root domain (e.g. `example.com`) and scopes it; `use ctlogs` loads the certificate-transparency module and `run` streams every subdomain found into the database (`run -j3` runs three module instances concurrently), each printed as it is added.

### Chain followup modules on the results

```
use dns-resolve
run
use url-scan
target where resolvable
run
```

Outcome: dns-resolve mass-resolves the collected subdomains, marking each resolvable or not and adding the IP addresses it finds; url-scan then restricts its target set to the resolvable ones (`target where resolvable`) and probes them, recording the live URLs and their HTTP status codes.

### Query the collected entities

```
select domains where value like %.com
```

Outcome: lists scoped entities matching the filter — every entity type (domains, subdomains, ipaddrs, urls, emails, accounts, devices, breaches, …) is queryable the same way, and `noscope`/`scope` with a where-clause moves entities out of and back into the active target set.

*No corpus evidence — derived from upstream documentation.*
