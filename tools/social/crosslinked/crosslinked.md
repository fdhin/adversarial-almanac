---
name: "crosslinked"
categories:
  - blackarch-social
version: "v0.3.0.r23.g2ae8d7b"
homepage: "https://github.com/m8r0wn/crosslinked"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "m8r0wn/crosslinked"
github-stars: 1640
github-pushed: "2024-11-26"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crosslinked

> LinkedIn employee enumeration via search-engine scraping: converts public profile results into formatted usernames and emails without LinkedIn credentials or API keys.

## What it's for

Given a company name (as it appears on LinkedIn) and a naming-format string, CrossLinked scrapes Google and Bing for that company's LinkedIn profiles and rewrites each real employee name into the account format the target organization uses — `{first}.{last}@domain.com`, `CORP\{f}{last}`, and so on. Because it never touches LinkedIn directly, no credentials or API keys are needed. Results land in `names.txt` (unique formatted accounts) and `names.csv` (name, job title, source URL), and the CSV can be re-parsed later under a different format without searching again. Index modifiers like `{1:last}` or `{-2:last}` handle multi-part names. Typical input to username-spraying and password-audit tooling in authorized engagements; increase timeouts, jitter and proxies when search engines start CAPTCHA-ing.

## Typical use cases

### Enumerate employees into email format

```
crosslinked -f '{first}.{last}@example.com' "Target Company"
```

Outcome: scrapes search engines for the company's LinkedIn profiles and writes `names.txt` with unique addresses in the requested format plus `names.csv` with the raw name/title/URL data.

### Generate Windows-style account names with throttling

```
crosslinked -f 'CORP\{f}{last}' -t 15 -j 2 "Target Company"
```

Outcome: same enumeration but emitting `CORP\jsmith`-style SAM account names, with a 15-second timeout per search and 2-second jitter between requests to stay under engine rate limits.

### Re-parse a previous scrape under a new format

```
crosslinked -f '{f}{last}@example.com' names.csv
```

Outcome: reformats the stored scrape data without issuing new searches — the way out when the naming convention turned out different mid-engagement or you are CAPTCHA-blocked.

### Rotate source proxies while scraping

```
crosslinked --proxy-file proxies.txt -f '{first}.{last}@example.com' -t 10 "Target Company"
```

Outcome: requests are distributed across the proxies listed one per line (`127.0.0.1:8080` for HTTP, `socks5://198.51.100.10:1080` style for SOCKS) so no single source address draws the engines' attention; a single proxy works via `--proxy`.

*No corpus evidence — derived from upstream documentation.*
