---
name: "sr"
categories:
  - blackarch-recon
version: "Version.r35.g6a17150"
homepage: "https://gitlab.com/Edu0x01/SR-71"
packaged-by: []
meta-checked: "2026-11-09"
worth: 0.0
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sr

> All-in-one Python recon driver (upstream name: SR-71) that enumerates a domain's subdomains and layers DNS, mail, port, WAF, takeover and leak checks on top in a single command.

## What it's for

Fast, broad reconnaissance of one domain without assembling a toolchain. A single run passively discovers subdomains and can additionally enumerate name servers, mail servers and extra DNS records, attempt a DNS zone transfer (AXFR), resolve the domain's IPv4/IPv6 addresses, identify the WAF and technology stack on the main page, check subdomains for takeover, probe the most common ports, look for common backup files, crawl for leaked secrets such as API keys, pull Wayback Machine endpoint history, and run a whois query. Each check is an opt-in flag; `--all` runs everything. It wraps public services and Python DNS queries rather than doing raw packet scanning, and it is not a vulnerability scanner.

## Typical use cases

### Enumerate a domain's subdomains

```
SR-71 -d example.com
```

Outcome: discovers subdomains via passive sources and prints them to the terminal. Installed as the `SR-71` console script (pip package `SR-71`); from a source checkout the equivalent is `python3 SR-71.py -d example.com`.

### Run every reconnaissance module at once

```
SR-71 -d target.example.com --all
```

Outcome: performs the full check suite — DNS/NS/MX enumeration, AXFR attempt, common-port scan, WAF detection, subdomain-takeover check, backup-file discovery, secrets crawl, technology identification, whois, Wayback endpoints — printing each section's findings as it completes.

### Pick specific checks

```
SR-71 -d target.example.com -n -p -w -b --whois --enum
```

Outcome: runs only the selected modules — here name servers, common-port scan, WAF detection, backup files, whois and technology identification. Any combination of the flags from `SR-71 --help` works.

### Save results without the banner

```
SR-71 -d target.example.com --output results.txt --quiet
```

Outcome: writes scan output to `results.txt` instead of stdout only; `--quiet` suppresses the ASCII banner so logs stay clean.

### Add Hunter.io email and employee discovery

```
SR-71 -d target.example.com -t TOKEN --all
```

Outcome: with a Hunter.io API token (free registration), the run additionally harvests mail accounts and employee names associated with the domain; without `-t` those lookups are skipped.

*No corpus evidence — derived from upstream documentation (GitLab Edu0x01/SR-71 README and embedded help panel).*
