---
name: "mailcat"
categories:
  - blackarch-social
version: "0.1.1"
homepage: "https://github.com/sharsil/mailcat/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-15"
build-system: python
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "sharsil/mailcat"
github-stars: 957
github-pushed: "2026-09-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 12
gh-activity-checked: "2026-11-09"
---

# mailcat

> Discover which mailboxes exist for a nickname across mail providers.

## What it's for

Mailcat (PyPI package `mailcat-osint`) takes a nickname and reports which email addresses built from it actually exist — checking 26 active providers covering 158+ domains (Gmail, Outlook/Hotmail, ProtonMail, Yahoo, Yandex, Mail.ru, Rambler, Zoho, Fastmail, Posteo, and more) via SMTP probes, unauthenticated registration flows, or provider APIs, with a couple of providers needing headless Chromium. It is the pivot step between a found username and a usable email target during OSINT; it does not access the mailboxes, only confirms existence. Runs accept positional names, files of usernames/emails, and Tor/proxy routing, which the upstream recommends since providers rate-limit by source IP.

## Typical use cases

### Find existing mailboxes for a nickname

```
mailcat username
```

Outcome: prints the providers where `username@<provider-domain>` exists, one result block per provider checked.

### Check a batch of usernames from a file

```
mailcat --file usernames.txt
```

Outcome: runs the same provider sweep for every line of the file (usernames and full email addresses both accepted, and extra positional names are merged in), printing a `[*] Checking username:` header before each result block.

### Route checks through Tor or a proxy

```
mailcat --tor username
```

Outcome: provider checks egress through the local Tor gateway (`--proxy http://198.51.100.10:8080` for a specific proxy instead), spreading the source IP and avoiding provider rate limits.

*No corpus evidence — derived from upstream documentation.*
