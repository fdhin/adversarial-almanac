---
name: "email2phonenumber"
categories:
  - blackarch-social
version: "29.9df9dbe"
homepage: "https://github.com/martinvigo/email2phonenumber"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "martinvigo/email2phonenumber"
github-stars: 2825
github-pushed: "2024-07-26"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# email2phonenumber

> OSINT tool that recovers a target's phone number from just an email address by chaining password-reset weaknesses — scrape exposed digits, generate valid candidates, correlate.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Martin Vigo's proof-of-concept for phone-number recovery starting from an email address. Sites that offer password reset by phone sometimes reveal masked fragments of that number; other sites confirm an email behind a phone through their own reset flow. The tool chains these weaknesses in three modes: `scrape` harvests partial digits via password resets, `generate` expands a mask like `555XXX1234` into every valid number allowed by the country's public numbering plan, and `bruteforce` walks candidates across sites that do not alert the target, matching their masked-email hints back to the victim's address. The author states the originally supported services (eBay, Lastpass, Amazon, Twitter) have long since added email codes and captchas — treat it as a demonstration of the methodology; the maintained successor for the generation side is Phonerator.

## Typical use cases

### Scrape partial phone digits via password reset

Harvest the phone-number fragments a target's email exposes on supported sites.

```
python3 email2phonenumber.py scrape -e user@example.com
```

Outcome: initiates password-reset flows that leak masked phone digits and prints every fragment recovered, which you combine into a mask for the next step.

### Generate valid phone numbers from a mask

Expand a partially-known number into all valid candidates.

```
python3 email2phonenumber.py generate -m 555XXX1234 -o /tmp/dic.txt
```

Outcome: writes every number permitted by the country's numbering plan to `/tmp/dic.txt`, one per line — the input dictionary for correlation.

### Correlate candidates back to the email

Determine which candidate number belongs to the target.

```
python3 email2phonenumber.py bruteforce -m 555XXX1234 -e user@example.com -p /tmp/proxies.txt -q
```

Outcome: iterates the candidate list, initiating password resets through the listed proxies to dodge captchas and abuse limits (`-q` keeps it quiet) and captures the masked-account hints returned; when a site's masked email matches the target's, the full phone number is identified.
