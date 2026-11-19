---
name: "phemail"
categories:
  - blackarch-social
version: "28.302b24d"
homepage: "https://github.com/Dionach/PhEmail"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Dionach/PhEmail"
github-stars: 349
github-pushed: "2019-09-16"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# phemail

> A python open source phishing email tool that automates the process of sending phishing emails as part of a social engineering test.

## What it's for

PhEmail automates the delivery side of an authorized phishing exercise:
it sends a phishing email to a list of recipients, tracks exactly who
opened it, and can pair the mailing with a cloned web page to capture
credentials. It also includes a harvesting engine that gathers email
addresses from LinkedIn via Google search results — useful for building
the target list in the first place — and supports authenticated Gmail
sending when the target domain blocks direct delivery. Use it only
against systems you have written permission to test.

## Typical use cases

### Send a tracked phishing campaign

Deliver a crafted email to a prepared recipient list.

```
./phemail.py -e emails.txt -f "Name Surname <name_surname@example.com>" -r "Name Surname <name_surname@example.com>" -s "Company Newsletter" -b body.txt
```

Outcome: Sends the body text with the given subject and spoofed FROM
address to every address in emails.txt, staggering messages by the
configured delay, and records which recipients clicked the tracking link
in a webserver log file.

### Harvest target addresses from LinkedIn via Google

Build the recipient list before the campaign.

```
./phemail.py -S "example" -d example.com -F 1 -p 12
```

Outcome: Searches Google for LinkedIn results matching the query, extracts
person names, generates addresses at example.com in the
`firstname.surname@example.com` format (`-F 1`), and saves the list.

### Clone a login portal for credential capture

Stand up a lookalike of the target's login page.

```
./phemail.py -c https://example.com/login
```

Outcome: Downloads and stores a local copy of the page so the phishing
link (`-w`) can point at the clone, which records submitted credentials.

### Send through Gmail authentication

Deliver when the target domain blacklists the source IP.

```
./phemail.py -e emails.txt -g user@gmail.com:Password1 -s "Subject" -b body.txt
```

Outcome: Authenticates to Gmail's SMTP service with the supplied
credentials and relays the campaign through it.

### Relay through a specific SMTP server

Point delivery at an internal or authorized relay.

```
./phemail.py -e emails.txt -m mail.example.com -s "Subject" -b body.txt
```

Outcome: Connects to the given SMTP server for all sends instead of
resolving the recipients' mail servers directly.

*No corpus evidence — derived from upstream documentation.*
