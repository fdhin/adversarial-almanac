---
name: "drupwn"
categories:
  - blackarch-webapp
version: "1.0.4.r2.g8186732"
homepage: "https://github.com/immunIT/drupwn"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "immunIT/drupwn"
github-stars: 617
github-pushed: "2020-11-04"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# drupwn

> Drupal enumeration & exploitation tool.

## What it's for

drupwn gathers Drupal intelligence in two modes: `enum` enumerates users, nodes, default files, modules and themes of a Drupal 7/8 site, and `exploit` checks the target against known Drupal CVEs and can exploit the ones it implements. Reach for it when a Drupal target is confirmed and you want both the inventory sweep and a CVE check in one tool; it supports cookies, custom User-Agents, HTTP basic auth, request delays, thread tuning, SOCKS/HTTP proxying and file logging, so it fits authenticated and rate-limited engagements. It does not fingerprint non-Drupal CMSs, and its CVE set reflects the 2018–2020 era it was last updated in.

## Typical use cases

### Full enumeration sweep

Run every enumeration against one target.

```
drupwn --mode enum --target target.example.com --users --nodes --modules --themes --dfiles
```

Outcome: walks user IDs, node IDs, module names, theme names and default Drupal files, printing each discovered entity; add `--log` to also write the output under `/tmp/` for keeping the evidence.

### Check a target for exploitable CVEs

Switch the tool into exploitation mode.

```
drupwn --mode exploit --target target.example.com
```

Outcome: runs the vulnerability checker against the target and, for CVEs it implements, offers/perform exploitation — the check-then-exploit flow for confirmed Drupal versions.

### Enumerate behind authentication and a proxy

Reach targets that need a session or an egress hop.

```
drupwn --mode enum --target target.example.com --users --cookies "PHPSESSID=abc123" --bauth user:Password1 --proxy socks://198.51.100.10:9050
```

Outcome: performs the sweep with the supplied session cookie, HTTP basic credentials and a SOCKS proxy for every request; `--thread` raises concurrency and `--delay` throttles requests when the target rate-limits.

### Update the enumeration data

Refresh bundled module/theme lists before a sweep.

```
drupwn --update
```

Outcome: pulls the latest module and theme name lists into the local installation so subsequent enum runs test current names, then exits.

No corpus evidence — derived from upstream documentation (README usage output).
