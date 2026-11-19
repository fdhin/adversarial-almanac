---
name: "dnswalk"
categories:
  - blackarch-recon
version: "2.0.2"
homepage: "http://sourceforge.net/projects/dnswalk/"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "davebarr/dnswalk"
github-stars: 36
github-pushed: "2022-09-09"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnswalk

> DNS zone debugger by David Barr: pulls a zone by AXFR and audits every record for consistency errors and bad practice.

## What it's for

dnswalk is a hostmaster's audit tool, not an attacker's enumerator. For each domain you point it at, it performs a zone transfer and then checks the database: broken or missing PTR records, CNAMEs pointing at CNAMEs, MX records to aliases, hosts with no PTR, lame delegations, duplicate A records and more. Findings are prefixed WARN (possible data problem), FAIL (could not access data) or BAD (invalid data), and the exit status equals the number of BAD errors, so it slots into scheduled zone-hygiene checks. It requires AXFR to be allowed from your host — it cannot audit zones that refuse transfer. The bundled do-dnswalk, makereports and sendreports scripts are templates for automating multi-zone runs and mailing per-hostmaster reports.

## Typical use cases

### Audit a zone

```
dnswalk example.com.
```

Outcome: transfers the zone and prints one diagnostic line per problem found, prefixed WARN/FAIL/BAD; the trailing dot on the domain is mandatory. The exit code equals the number of BAD errors, so `dnswalk example.com. > report.txt` captures the full audit for review.

### Recurse into subdomains

```
dnswalk -r example.com.
```

Outcome: repeats the audit for every delegated subdomain encountered inside the zone — use with care on deep hierarchies.

### Check forward/reverse consistency ("fascist" mode)

```
dnswalk -F example.com.
```

Outcome: for every A record, resolves the PTR for that IP and reports mismatches between the forward name and the reverse name — the classic cause of refused FTP and mail sessions.

### Check for lame delegations

```
dnswalk -l example.com.
```

Outcome: verifies that each server listed in an NS record actually returns authoritative answers for the zone and reports the ones that do not.

*No corpus evidence — derived from upstream documentation.*
