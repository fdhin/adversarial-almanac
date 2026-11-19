---
name: "goog-mail"
categories:
  - blackarch-recon
version: "1.0"
homepage: "http://www.darkc0de.com/others/goog-mail.py"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BushidoUK/Goog-mail"
github-stars: 7
github-pushed: "2021-04-06"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# goog-mail

> Minimal darkc0de-era Python 2 script that scrapes Google results for a domain and prints every email address found.

## What it's for

goog-mail.py is a one-argument email harvester: it pages through Google search results for the domain you pass, strips the HTML, and accumulates every string that looks like an email at that domain — the quick-and-dirty way to seed a phishing target list or find role accounts (`info@`, `admin@`, `hr@`) that osint databases miss. It originates from the darkc0de script archive and has no author attribution; the linked GitHub repository is a third-party re-upload of the same script, and the code is Python 2 (`httplib`/`urllib2`). Being a plain scraper, modern Google bot detection will throttle repeated runs, so treat it as a single-shot helper for small domains rather than a bulk collector.

## Typical use cases

### Harvest email addresses for a domain

```
goog-mail example.com
```

Outcome: iterates Google result pages for the domain, extracts and deduplicates matching email addresses, and prints the collected list — feed it into a contact-mapping or phishing-simulation workflow. The same script runs as `python2 goog-mail.py example.com` from a source checkout.

*No corpus evidence — derived from upstream documentation.*
