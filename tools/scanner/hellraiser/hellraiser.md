---
name: "hellraiser"
categories:
  - blackarch-scanner
version: "279.bea43e2"
homepage: "https://github.com/m0nad/HellRaiser"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: node
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "m0nad/HellRaiser"
github-stars: 585
github-pushed: "2023-03-08"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hellraiser

> Self-hosted Rails web app that runs nmap scans and matches each discovered product (CPE) against a cve-search database to enumerate vulnerabilities per host.

## What it's for

HellRaiser (m0nad) is a vulnerability-management console you run yourself: you enter targets in the web UI, it nmap-scans them, derives CPE identifiers from the versions found, and correlates those CPEs with CVEs via a cve-search API instance you also host (endpoint configured in `config/config.yml`). It is a coordinated stack — a Ruby on Rails application plus Redis-backed workers, started together with foreman — rather than a one-shot CLI scanner. Database setup, bundle installation and cve-search deployment are installation concerns (see install.md); the packaged `hellraiser` command only boots the stack.

## Typical use cases

### Launch the scanner web interface

Start every component of the app at once.

```
hellraiser
```

Outcome: foreman starts the Rails server and its workers (from a source checkout the equivalent command is `foreman s`); open `http://127.0.0.1:3000`, add targets, and browse the per-host CVE results once scans complete.

*No corpus evidence — derived from upstream documentation.*
