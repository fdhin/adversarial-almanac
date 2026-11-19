---
name: "whoxyrm"
categories:
  - blackarch-recon
version: "1.0.0.r5.g77318a7"
homepage: "https://github.com/MilindPurswani/whoxyrm"
packaged-by: [blackarch]
blackarch-builddate: "2024-07-05"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "MilindPurswani/whoxyrm"
github-stars: 175
github-pushed: "2024-03-31"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# whoxyrm

> Reverse-whois lookups through the Whoxy API: find every domain matching a registrant organization, name, email or keyword.

## What it's for

whoxyrm is a Go client for the Whoxy reverse-whois API, built for the OSINT step of mapping an organization's external footprint: instead of asking "who owns this domain?", you ask "which domains were registered by this company, person or email?" and get the full list, with pagination through result pages handled automatically. It needs a Whoxy API key (free tier available at whoxy.com) exported as `WHOXY_API_KEY` before it will run at all. It only queries Whoxy's data — it does not enumerate a domain's subdomains or do DNS lookups; pair it with certificate-transparency or DNS tools for infrastructure discovery.

## Typical use cases

### Find domains registered by an organization

Map a company's registered estate for perimeter or phish-domain hunting.

```
export WHOXY_API_KEY="your-api-key"
whoxyrm -company-name "Example Inc."
```

Outcome: queries the Whoxy API for all registrations whose registrant organization matches `Example Inc.`, walking every result page, and prints the matching domains (creation date, registrant fields as returned by the API). `-name "Example Inc."` matches the registrant name field the same way.

### Pivot from one known contact email

Enumerate other registrations tied to a registrant email you already have.

```
whoxyrm -email "user@example.com"
```

Outcome: returns every domain whose registration record lists that email address — a standard pivot from WHOIS data obtained elsewhere to additional attacker-relevant assets.

### Search registrations by keyword

Broaden the net when the exact registrant string is unknown.

```
whoxyrm -keyword "example"
```

Outcome: performs a Whoxy keyword search across registration records and lists all matching domains with their WHOIS summary; expect more noise than the organization- or email-scoped queries.
