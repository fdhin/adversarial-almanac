---
name: "trevorspray"
categories:
  - blackarch-cracker
version: "2.3.0"
homepage: "https://github.com/blacklanternsecurity/TREVORspray"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-15"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "blacklanternsecurity/TREVORspray"
github-stars: 1401
github-pushed: "2026-10-26"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 14
gh-activity-checked: "2026-11-09"
---

# trevorspray

> Modular password sprayer for Microsoft 365 and other identity providers, with SSH-proxy source rotation.

## What it's for

Sprays one or a few passwords across many accounts — the pattern that evades per-account lockout counters — instead of many passwords at one account. The default module targets Microsoft 365 / MSOL; pluggable spray modules cover OWA, ADFS, Okta, Auth0, JumpCloud and Cisco AnyConnect. It pairs spraying with tenant recon (MX records, Azure/ADFS endpoints, username enumeration), can round-robin every request through a pool of SSH hosts so no single source IP carries the whole spray, reconnects automatically when a proxy dies, remembers already-tried user/password combos across runs, and executes "loot" follow-up activity against accounts that authenticate. This is the v2+ rewrite by Black Lantern Security of arkadiyt's original sprayer, and is now the canonical upstream.

## Typical use cases

### Recon a domain first

Discover where and how to spray before touching logins.

```
trevorspray --recon example.com
```

Outcome: prints tenant and authentication metadata — MX records, Azure/ADFS/autodiscover info and the OAuth token endpoint — which you feed back via `--url` when the default endpoint is not the right one; with users supplied it additionally enumerates valid usernames.

### Spray a password list at an email list

The core campaign: a couple of passwords across a whole account list.

```
trevorspray -u emails.txt -p 'Password1' 'Spring2026!'
```

Outcome: attempts each password against every account and reports per-account status (valid, nonexistent, locked, MFA-required); valid logins are highlighted, remembered under `~/.trevorspray/` and loot modules run against them unless `--no-loot` is set.

### Rotate source IPs over SSH proxies

Spread the spray across pivots so blocks and lockouts do not pile onto one address.

```
trevorspray -u emails.txt -p Password1 --ssh user@198.51.100.10 --ssh user@198.51.100.11 -n
```

Outcome: tunnels requests round-robin through the given SSH hosts (`-n` excludes your own IP); dropped proxies reconnect and the spray resumes, with `-v` logging which proxy served each request.

### Slow down to survive lockout policies

Turn a spray into a low-and-slow campaign.

```
trevorspray -u emails.txt -p Password1 --delay 5 --jitter 30 --lockout-delay 900
```

Outcome: sleeps 5 seconds (plus random jitter up to 30) between requests and pauses an extra 900 seconds whenever a lockout signal is seen, keeping the run under lockout and rate-limit thresholds.

*No corpus evidence — derived from upstream documentation.*
