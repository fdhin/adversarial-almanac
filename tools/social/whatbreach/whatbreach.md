---
name: "whatbreach"
categories:
  - blackarch-social
version: "42.dad6b9f"
homepage: "https://github.com/ekultek/whatbreach"
packaged-by: [blackarch]
blackarch-builddate: "2021-02-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ekultek/whatbreach"
github-stars: 1698
github-pushed: "2025-08-14"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# whatbreach

> Looks up which breaches an email appeared in (HIBP) and offers dehashed database links and paste downloads — now mostly historic.

## What it's for

WhatBreach (Ekultek) is a Python 2 breach-lookup CLI for email-centric OSINT: give it one address (or a file of them) and it queries Have I Been Pwned for every breach and paste the address appears in, renders a table mapping each breach to a Dehashed search link for obtaining the database, optionally downloads publicly available databases via databases.today, downloads the raw Pastebin pastes, and enriches with hunter.io (deliverability, domain emails — free API token) and emailrep.io profile hints. It also detects ten-minute-mail domains and can throttle requests to avoid HIBP blocking. Age caveat, stated plainly: several of those backends no longer exist — weleakinfo.com was seized by law enforcement in January 2020 and databases.today is gone — and HIBP's API now requires a paid key (about USD 3.50/month), so the tool's core lookup works only after you buy that key, and the Python 2 runtime itself is legacy. Use it as a quick breach-index viewer when you already hold an HIBP key; for current research, querying HIBP, Dehashed and emailrep directly is more reliable.

## Typical use cases

### Look up every breach for one email

```
whatbreach -e user@example.com
```

Outcome: after searching HIBP for breaches and pastes, prints a table of each breach/paste name with its Dehashed search link (e.g. `https://www.dehashed.com/search?query=LinkedIn`); needs your HIBP situation resolved first — the tool era predates mandatory keys.

### Sweep a list of addresses with throttling

```
whatbreach -f emails.txt --throttle 5 -cT
```

Outcome: processes one email per line from the file, pausing ~5 seconds between HIBP requests to avoid blocking, and first flags each address that belongs to a ten-minute-mail provider (`-cT`) so disposables can be skipped.

### Collect pastes and databases for follow-up

```
whatbreach -e user@example.com -dP -d -s breach-downloads
```

Outcome: downloads the raw Pastebin pastes the email appeared in (`-dP`) and attempts to fetch each publicly available breached database (`-d`) into `breach-downloads/` instead of your home directory — historical behavior, since the paste/database sources of the tool's era are defunct.

### Verify deliverability and pivot to the domain

```
whatbreach -e user@example.com -sH -vH
```

Outcome: with a configured hunter.io token, searches the email's domain for further addresses it can process (`-sH`) and checks each found address for deliverability (`-vH`), extending a one-address lead into a domain-wide pass.

*No corpus evidence — derived from upstream documentation.*
