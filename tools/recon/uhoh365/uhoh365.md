---
name: "uhoh365"
categories:
  - blackarch-recon
version: "26.110277a"
homepage: "https://github.com/Raikia/UhOh365"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "Raikia/UhOh365"
github-stars: 610
github-pushed: "2021-03-02"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# uhoh365

> Validate which email addresses exist in Office 365 with zero login attempts, via the unauthenticated Autodiscover status-code side channel.

## What it's for

UhOh365 (by Chris King, @raikiasec) enumerates valid Office 365 mailboxes without a single authentication attempt. It exploits Microsoft's Autodiscover endpoint (`/autodiscover/autodiscover.json/v1.0/{EMAIL}?Protocol=Autodiscoverv1`): a 200 response means the mailbox exists, a 302 means it does not — no credentials, no login event, nothing visible to the mailbox owner. That makes it the email-validation step for social-engineering engagements: feed it a candidate list and it splits valid from invalid silently. The author measured it as effectively unthrottled (2,000+ validations in a minute), it is threaded (default 20), and it supports routing through a proxy or disabling SSL verification for speed. It validates existence only — no passwords, no mailbox access. Microsoft does not treat email enumeration as a vulnerability, but the endpoint behavior can change; verify on your own tenant before relying on it at scale.

## Typical use cases

### Validate a list of full email addresses

Split an O365 candidate list into valid/invalid.

```
uhoh365 emails.txt
```

Outcome: every line of emails.txt is checked against Autodiscover with 20 threads; by default only VALID addresses are printed to the terminal — the confirmed-deliverable subset for a phishing pretext or user census.

### Expand bare usernames to a domain

Check usernames against one tenant without typing full addresses.

```
uhoh365 usernames.txt -s example.com
```

Outcome: `-s/--suffix` appends @example.com to each input line before validation, so a harvested username list becomes an O365 mailbox list in one pass; output is again the valid addresses only.

### Save valid addresses and show the misses too

Capture results and audit the negative space.

```
uhoh365 emails.txt -v -o valid.txt
```

Outcome: `-v/--verbose` prints each address with its valid/invalid verdict (useful for spot-checking known-good and known-bad seeds), while `-o/--output valid.txt` writes only the valid ones to disk for the next engagement phase.

### Tune throughput and route through a proxy

Control speed and visibility of the enumeration.

```
uhoh365 emails.txt -t 50 -p http://127.0.0.1:8080
```

Outcome: raises the thread count to 50 for faster sweeps and sends all Autodiscover requests through the given HTTP proxy (e.g. to log or scope them); `-n/--nossl` additionally disables SSL verification when speed matters more than transport integrity.

*No corpus evidence — derived from upstream documentation.*
