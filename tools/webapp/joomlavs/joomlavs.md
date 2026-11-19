---
name: "joomlavs"
categories:
  - blackarch-webapp
version: "254.eea7500"
homepage: "https://github.com/rastating/joomlavs"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "rastating/joomlavs"
github-stars: 274
github-pushed: "2018-07-05"
github-archived: true
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# joomlavs

> Ruby black-box scanner that enumerates vulnerable Joomla components, modules and templates.

## What it's for

JoomlaVS (by rastating) automates assessing how exploitable a Joomla installation is: it fingerprints the Joomla version and scans for known-vulnerable components, modules and templates as well as core Joomla vulnerabilities. It is black-box — no credentials or host access needed, just a URL — and each scan category can be run separately or all at once. The upstream repository is archived (last push 2018), so its extension database has been frozen since; for current Joomla work the OWASP joomscan (this vault, the family flagship) is the maintained sibling, with jooforce covering admin-login brute force, jomplug component fingerprinting, and joomlascan legacy RFI path probes.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Full scan of a Joomla site

Run every enumeration category against a target.

```
joomlavs -u https://target.example.com --scan-all
```

Outcome: fingerprints the Joomla instance, then enumerates vulnerable components, modules and templates and core issues, printing each finding with the matching vulnerability reference; exit summary lists what was detected.

### Enumerate one category passively

Check only components, without sending active probes.

```
joomlavs -u https://target.example.com --scan-components --quiet
```

Outcome: only the component category runs, and `--quiet` restricts the scan to passive methods — useful against fragile or monitored targets where you want enumeration without exploit-shaped requests.

### Scan through a proxy with tuning

Route and parallelize the scan.

```
joomlavs -u https://target.example.com --scan-all --proxy socks5://127.0.0.1:9050 --threads 20
```

Outcome: all requests go through the SOCKS5 proxy (HTTP, SOCKS4, SOCKS4A and SOCKS5 are supported) with 20 concurrent threads, shortening large enumerations; `--proxy-auth user:pass`, `--user-agent`, `--follow-redirection` and `--disable-tls-checks` tune the same run.
