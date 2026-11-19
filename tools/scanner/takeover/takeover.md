---
name: "takeover"
categories:
  - blackarch-scanner
version: "98.a058647"
homepage: "https://github.com/m4ll0k/takeover"
packaged-by: [blackarch]
blackarch-builddate: "2023-05-05"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "m4ll0k/takeover"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# takeover

> Subdomain takeover finder: flags CNAMEs pointing at de-provisioned SaaS services you could claim.

## What it's for

Takeover (v0.2, by m4ll0k) takes a domain or a list of subdomains,
resolves them, and checks whether each points at a third-party service —
GitHub Pages, AWS/S3, Heroku, Shopify, Zendesk, and roughly forty more —
whose account has been removed. A dangling CNAME of that shape is a
subdomain takeover: anyone who re-registers the service slot inherits the
victim's hostname. The tool prints which service each vulnerable
subdomain dangles from; it does not attempt the claim itself, and it does
not discover subdomains — feed it output from your own enumeration
(passive DNS, bruteforce, certificate transparency). The upstream
repository is gone; the packaged copy installs `takeover.py` as
`takeover`.

## Typical use cases

### Check a single domain

Quick verdict on one hostname.

```
takeover -d www.example.com -v
```

Outcome: resolves the hostname, follows its CNAME chain, and reports each
service it lands on as takeoverable or not; `-v` prints the response
fingerprints behind each verdict.

### Sweep a subdomain list to a report file

Process enumeration output in bulk.

```
takeover -l subdomains.txt -o output.txt -v
```

Outcome: every subdomain in the list is checked and the results are
written to the output file; `-o output.json` produces JSON instead, and
`-T 10` raises the thread count for large lists.

### Route checks through a proxy

Match your egress IP to the engagement's.

```
takeover -d www.example.com -p http://198.51.100.10:8080 -v
```

Outcome: all service lookups go through the given HTTP proxy; slow links
can also take `-t 30` to raise the per-request timeout.

*No corpus evidence — derived from upstream documentation (archived README, v0.2).*
