---
name: "subover"
categories:
  - blackarch-scanner
version: "71.3d258e2"
homepage: "https://github.com/Ice3man543/SubOver"
packaged-by: [blackarch]
blackarch-builddate: "2021-11-24"
build-system: go
license: BSD
meta-checked: "2026-11-09"
github-repo: "Ice3man543/SubOver"
github-stars: 968
github-pushed: "2023-10-17"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# subover

> Discontinued hostile-subdomain-takeover checker for ~50 cloud services.

## What it's for

SubOver (Ice3man543) takes a list of subdomains and matches their CNAMEs
against a fingerprint database of roughly 50 takeover-vulnerable services —
GitHub, Heroku, Shopify, AWS S3/CloudFront, Azure, Zendesk, Fastly, Pantheon
and more — reporting where a dangling record could be claimed. Written in Go
for concurrency (`-t`), with `-https` to force TLS, `-a` to probe every host
rather than only CNAME-bearing ones, `-v` for verbose detail and `-timeout`
per request. The fingerprints live in providers.json beside the binary and
must stay in place or the tool stops working. The project is discontinued
upstream (2018-era), so its service coverage is frozen — prefer a maintained
takeover scanner for current targets.

## Typical use cases

### Check a subdomain list

Standard takeover sweep.

```
subover -l subdomains.txt -t 20
```

Outcome: each subdomain's CNAME is matched against providers.json and
potential takeovers are printed with the matching service name.

### Probe every host over HTTPS

Catch CNAME-less dangling services at the cost of noise.

```
subover -l subdomains.txt -a -https -v
```

Outcome: requests are forced to HTTPS and sent to every host in the list
regardless of CNAME — slower and more false-positive-prone — with verbose
per-request detail printed.

*No corpus evidence — derived from upstream documentation
(Ice3man543/SubOver README).*
