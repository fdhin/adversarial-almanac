---
name: "buster"
categories:
  - blackarch-social
version: "92.131437e"
homepage: "https://github.com/sham00n/buster"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "sham00n/buster"
github-stars: 1441
github-pushed: "2020-03-26"
github-archived: false
github-issues: 33
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# buster

> OSINT email reconnaissance: generate candidate addresses from names, patterns or usernames, verify which exist, and enumerate the social accounts, breaches and pastes attached to them.

## What it's for

Given a name, a username, or a partially masked email pattern (as leaked profiles sometimes show), Buster builds candidate addresses and checks them against a hundred-plus providers, then pulls everything publicly attached to the valid ones: social registrations (Gravatar, GitHub, Skype, about.me, ...), breach records, paste-site appearances, and reverse-whois domain registrations. Validations draw on external services with a free quota (about 200 email validations per day) and company-email lookups need a hunter.io API key placed in `api-keys.yaml`. Use it for the email phase of an OSINT engagement; it does not attempt credential guessing or account access.

## Typical use cases

### Profile a single known address

```
buster -e target@example.com
```

Outcome: reports whether the address exists, which social accounts were registered with it, which breaches contain it, and links to pastes/pages where it appears.

### Complete a masked pattern into real addresses

Use when a leak shows a censored pattern instead of the full address.

```
buster -e 'j********9@g****.com' -f john -l doe -b ****1989
```

Outcome: generates candidates consistent with the masked pattern plus the name and birthdate hints, validates each, and lists the live ones with their associated information.

### Expand a username into mailboxes across providers

```
buster -u johndoe
```

Outcome: checks `johndoe@<provider>` across 100+ email providers and reports which exist; append `-p gmail.com` to restrict generation to specific domains.

### Generate and validate a naming scheme

```
buster -f john -m james -l doe -p gmail.com yahoo.com -v
```

Outcome: emits every address permutation for the name across the given providers; with `-v` each one is validated and profiled, without it the candidates are just printed (add `-o usernames.txt` to write them to a file for other tools).

### Batch-process a list of addresses

```
buster --list emails.txt -o results.txt
```

Outcome: runs the full validation and enrichment pass over every address in the file and writes the findings to the output file.

### Resolve a corporate email via hunter.io

```
buster -f john -l doe -c example.com
```

Outcome: returns the most likely work email for that name at the company domain (hunter.io lookup — set the API key first) along with the information associated with it.

*No corpus evidence — derived from upstream documentation.*
