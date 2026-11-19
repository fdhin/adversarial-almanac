---
name: "adidnsdump"
categories:
  - blackarch-recon
version: "v1.6.0.r0.gc6bff9a"
homepage: "https://github.com/dirkjanm/adidnsdump"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-24"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "dirkjanm/adidnsdump"
github-stars: 1210
github-pushed: "2026-10-24"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# adidnsdump

> Dumps all records from an Active Directory-integrated DNS zone over LDAP with any authenticated domain account — the zone transfer AD never allows.

## What it's for

AD-integrated DNS zones store their records in the directory, and by default any authenticated user can read them via LDAP — effectively a zone transfer without DNS-level permission. adidnsdump exploits exactly that: it enumerates every record in the domain's DNS zones (optionally forest-wide or from the legacy System partition) and writes them to CSV for internal-network recon — hostnames, subdomains, and stale records that reveal infrastructure. It can also resolve records that LDAP hides behind wildcard obfuscation by querying DNS directly. It needs only a standard domain account (password, hash, or Kerberos) and speaks plain LDAP, LDAPS, or DNS over TCP through a pivot. It collects only; it modifies nothing.

## Typical use cases

### Dump the domain DNS zone

Pull every record from the target domain's DomainDnsZones.

```
adidnsdump -u 'example.com\user' dc.example.com
```

Outcome: prompts for the password, enumerates the zone via LDAP, and writes `records.csv` with every DNS record (A, CNAME, MX, SRV, TXT, ...) — an internal hostname inventory from a single unprivileged account.

### Recover records hidden behind a wildcard

Defenders sometimes replace records with a wildcard so LDAP listing shows junk; force resolution of the real names.

```
adidnsdump -u 'example.com\user' -r dc.example.com
```

Outcome: after listing the zone, the tool queries DNS for each discovered name to resolve its actual address despite wildcard masking, so `records.csv` reflects real targets rather than the wildcard's boilerplate answer.

### Enumerate every zone on the server

Discover which DNS zones exist before choosing a target.

```
adidnsdump --print-zones -u 'example.com\user' dc.example.com
```

Outcome: lists all zones hosted on the DNS server and exits — a quick read-only check to find non-default zones worth dumping with `--zone <name>`, `--forest`, or `--legacy`.

### Dump DNS through a pivot

Work from an implant tunnel that only passes TCP.

```
adidnsdump --dns-tcp -u 'example.com\user' dc.example.com
```

Outcome: DNS resolution performed by the tool (e.g. with `-r`) runs over TCP instead of UDP, which survives proxychains-style tunnels; LDAP traffic for the zone dump itself flows through the same pivot.

*No corpus evidence — derived from upstream documentation.*
