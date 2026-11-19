---
name: "metabigor"
categories:
  - blackarch-recon
version: "79.c0a3ecf"
homepage: "https://github.com/j3ssie/metabigor"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2025-07-05"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "j3ssie/metabigor"
github-stars: 1860
github-pushed: "2026-08-08"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---

# metabigor

> OSINT infrastructure mapping — networks, subdomains, ports, related domains — with no API keys.

## What it's for

Metabigor (by j3ssie of the Osmedeus project) maps a target's footprint from free sources only: which CIDRs sit behind an ASN or organization, which subdomains appear in certificate transparency logs, which ports and CVEs an IP exposes (Shodan InternetDB, keyless), which domains are related, and which addresses hide behind a CDN. Every command reads targets as arguments, `-i`, `-I <file>` or stdin, and writes to stdout with `-f text|flat|json|csv` controlling shape, so results pipe straight into the next command. It does not exploit anything or touch the target directly — it queries public data and a bundled offline IP-to-ASN database. Note the v3 CLI renamed several commands and flags from v2 (e.g. `ipc` → `cluster`, `--json` → `-f json`). No corpus evidence — derived from upstream documentation.

## Typical use cases

### List the networks behind an ASN

Turn an AS number into concrete CIDR ranges.

```
metabigor net AS13335
```

Outcome: prints every CIDR announced by that ASN from the bundled offline database (refresh it with `metabigor update`), one per line — the ranges to feed a port scanner.

### Find networks by organization name

Start from a company name instead of an ASN.

```
metabigor net --org Cloudflare --detail
```

Outcome: searches the local database by organization and lists matching CIDRs with the owning ASN, organization name, and country in the `--detail` columns; `--live` switches to live BGP sources instead of the bundled database.

### Harvest subdomains from certificate logs

Enumerate a domain via certificate transparency.

```
metabigor cert example.com
```

Outcome: queries crt.sh and prints discovered subdomains one per line (wildcard entries included); `--clean` strips `*.` prefixes, `--wildcard` keeps only wildcard entries, and `--detail` adds certificate IDs, issuers and validity dates. Pipe into a DNS resolver to find live hosts.

### Enrich IPs with ports and CVEs

Learn what an address or range exposes.

```
metabigor ip 198.51.100.0/24 -f flat
```

Outcome: expands the CIDR and queries the free Shodan InternetDB for each host, printing `IP:port` pairs in flat format; the default text format adds hostnames and known CVEs per host.

### Chain lookups into a pipeline

Feed one command's output into the next.

```
metabigor net AS13335 -f flat | metabigor ip -f flat
```

Outcome: takes the CIDR list from `net`, enriches every address through `ip`, and prints the combined `IP:port` list — no intermediate files needed. The same pattern works with `cert` and `related` feeding domain consumers, or with external tools such as dnsx or httpx on either side of the pipe.

### Discover related domains

Pivot from one domain to others owned by the same organization.

```
metabigor related example.com --sources crt,whois
```

Outcome: correlates certificate logs, reverse WHOIS and shared analytics IDs (sources chosen with `--sources`, default `all`) and prints deduplicated related domains tagged with the source that found them first.
