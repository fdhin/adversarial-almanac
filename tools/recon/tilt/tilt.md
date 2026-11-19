---
name: "tilt"
categories:
  - blackarch-recon
version: "90.2bc2ef2"
homepage: "https://github.com/AeonDave/tilt"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "AeonDave/tilt"
github-stars: 55
github-pushed: "2014-05-16"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tilt

> Terminal IP Lookup Tool — one-shot passive reconnaissance for a host or IP: DNS, GeoIP, whois and reverse-IP in a single command.

## What it's for

TILT (Terminal IP Lookup Tool) is a Python 2 script for first-pass passive reconnaissance of a hostname or IP: host-to-IP and IP-to-host conversion, GeoIP translation, whois data (registrar, dates, name servers, owner), subdomains, SOA and DNS records, and reverse IP lookup that lists hosts sharing the target's address. Everything is queried from third-party lookup services, so the target itself is never contacted. The upstream repository has been archived since 2014 and the tool predates modern resolver APIs — several of the web services it scraped have changed or disappeared, so treat specific result sections (GeoIP, Google search) as best-effort and rely on it as a quick aggregator rather than a maintained scanner.

## Typical use cases

### Quick passive profile of a hostname

Resolve and profile a domain without touching its servers.

```
tilt -t example.com
```

Outcome: prints the resolved IP, GeoIP translation, whois summary (registrar, dates, name servers), DNS/SOA records and any subdomains the lookup services report — a one-screen passive overview.

### Reverse IP lookup on an address

Find hostnames that share a target IP.

```
tilt -t 198.51.100.10 -r
```

Outcome: performs a reverse IP lookup and lists other hostnames resolving to the same address; combine with `-e` (`--extensive`) to also look for hosts that resolve to different IPs on the same machine.

### Extensive recon saved to a log file

Run every lookup and keep the output.

```
tilt -t example.com -e -r -o recon.log
```

Outcome: runs the extensive lookup plus reverse IP and writes the full log to recon.log while still printing to the terminal; `-o` is how you keep evidence of the pass for later comparison.

*No corpus evidence — derived from upstream documentation.*
