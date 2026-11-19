---
name: "weebdns"
categories:
  - blackarch-recon
version: "14.c01c04f"
homepage: "https://github.com/WeebSec/weebdns"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "WeebSec/weebdns"
github-stars: 45
github-pushed: "2018-10-30"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# weebdns

> Asynchronous DNS record enumerator: dumps the full record set for one host through a local async resolver or a public DNS-over-HTTPS API.

## What it's for

WeebDNS (WeebSec/FuzzyRabbit) is a single-target DNS interrogation tool: give it one host and it asynchronously queries the whole standard record set — A, AAAA, CNAME, MX, NS, SOA, SRV, TXT (plus NAPTR locally, or rarer types like CAA, DNSKEY, TLSA, SSHFP via the Google resolver) — printing labeled sections per type with TTLs. The default path uses a local aiodns resolver against your system nameservers; three flags switch the query source to a public JSON API instead (`--dnsapi` for dns-api.org, `--exana` for exana.io, `--google` for dns.google.com), which changes both the record coverage and where the queries appear to come from. It enumerates records for a name you already know — it is not a subdomain brute-forcer (see tools like waldo or massdns for that), and it performs no zone transfers.

Commands below are verified against the upstream source (`weebdns.py` argument parser).

## Typical use cases

### Dump all DNS records for a host

Get the complete record picture from your default resolver.

```
python3 weebdns.py https://example.com
```

Outcome: resolves `example.com` (the `http://`/`https://` prefix is required — the tool extracts the hostname from the URL) and prints one colored section per record type with each value and TTL, or `Record Not Found: <type>` for absent types.

### Query through a public DNS API

Interrogate the name via a third-party resolver for broader record coverage.

```
python3 weebdns.py https://example.com --google
```

Outcome: asks `dns.google.com/resolve` for every type it supports (including CAA, DS, RRSIG, TLSA and other rarely-served records) and prints the same labeled sections from the JSON answers — queries originate from Google's resolver rather than your machine. `--dnsapi` and `--exana` select the other two API backends the same way (only one source per run).

*No corpus evidence — derived from upstream documentation.*
