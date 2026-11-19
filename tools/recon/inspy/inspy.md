---
name: "inspy"
categories:
  - recon
version: "3.0.0-0parrot1"
homepage: "https://github.com/gojhonny/InSpy"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "gojhonny/InSpy"
github-stars: 577
github-pushed: "2023-07-11"
github-archived: false
github-issues: 29
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# inspy

> LinkedIn enumeration: harvest employees by job-title keywords and derive their email addresses.

## What it's for

InSpy (gojhonny, by Jonathan Broche) automates LinkedIn enumeration against a named company: with a titles/departments wordlist it scrapes matching employees, and combined with a domain and an email format (first.last@, flast@ and similar) it constructs candidate corporate addresses. A Hunter.io API key must be pasted into the script source for email-hunting support. The upstream repository is marked deprecated and LinkedIn's anti-scraping measures make live runs unreliable — treat it as a template for LinkedIn OSINT automation rather than a dependable daily driver.

## Typical use cases

### Enumerate employees by title keyword

Use the bundled title wordlist to find staff matching technologies or roles.

```
inspy example-corp --titles title-list-small.txt --csv employees.csv
```

Outcome: scrapes LinkedIn for the company and writes matching employees with their titles or departments to `employees.csv`; the default wordlist is used when `--titles` is omitted.

### Derive email addresses for found staff

Combine a known domain and email pattern with the harvest.

```
inspy example-corp --domain example.com --email first.last@example.com --json employees.json
```

Outcome: outputs the enumerated employees plus constructed addresses in the given format to `employees.json`; HTML, CSV and XML export are also available via the respective output flags.

*No corpus evidence — derived from upstream documentation.*
