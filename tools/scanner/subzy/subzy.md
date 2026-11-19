---
name: "subzy"
categories:
  - blackarch-scanner
version: "v1.2.1.r0.gf2e2ec7"
homepage: "https://github.com/PentestPad/subzy"
packaged-by: [blackarch]
blackarch-builddate: "2025-08-07"
build-system: go
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "PentestPad/subzy"
github-stars: 1612
github-pushed: "2024-09-10"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# subzy

> Fingerprint-based subdomain takeover checker built on can-i-take-over-xyz.

## What it's for

Subzy (now maintained under the PentestPad org) detects subdomain takeovers
by matching each subdomain's HTTP response against fingerprints derived from
the can-i-take-over-xyz list of vulnerable services. Targets come as a single
host, a comma-separated set (`--target`), or a file (`--targets`); checks run
concurrently with tunable `--concurrency`, `--timeout` and `--verify_ssl`,
`--https` defaults to TLS when a target carries no protocol, and
`--hide_fails` suppresses non-vulnerable results. The fingerprint database
is refreshable with the `update` subcommand, so detection coverage can track
the upstream list. Every subcommand has a one-letter alias (`run`/`r`,
`update`/`u`, `version`/`v`).

## Typical use cases

### Check one or more specific subdomains

Quick targeted check.

```
subzy run --target test.example.com,https://other.example.com
```

Outcome: each target is fetched and fingerprint-matched; vulnerable
subdomains are reported with the takeoverable service, and non-vulnerable
ones are listed as failed checks unless `--hide_fails` is set.

### Check a file of subdomains

The usual bulk mode.

```
subzy run --targets subdomains.txt --concurrency 20 --https
```

Outcome: the whole list is checked concurrently, defaulting to HTTPS for
entries without an explicit protocol, with a summary of findings at the end.

### Refresh the fingerprint database

Keep detections current.

```
subzy update
```

Outcome: the local copy of the service fingerprints is refreshed from
upstream, adding newly recognized takeover signatures to future runs.

*No corpus evidence — derived from upstream documentation (PentestPad/subzy
README).*
