---
name: "cloudflare-enum"
categories:
  - blackarch-scanner
version: "10.412387f"
homepage: "https://github.com/mandatoryprogrammer/cloudflare_enum"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "mandatoryprogrammer/cloudflare_enum"
github-stars: 525
github-pushed: "2022-08-06"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cloudflare-enum

> Recovers a domain's DNS records by adding it to your own Cloudflare account and dumping Cloudflare's DNS archive.

## What it's for

cloudflare_enum (mandatoryprogrammer) turns a Cloudflare onboarding feature into a recon technique: when a domain is added to a Cloudflare account, Cloudflare displays the DNS records it already knows for it — including subdomains and origin addresses that may sit behind Cloudflare's proxy. The tool logs into a Cloudflare account you control, adds the target domain, and prints every A/CNAME record Cloudflare has archived for it. It requires a working Cloudflare account that can add domains, and it is python2-era code. It enumerates DNS only — no port scanning, content discovery or bypass of Cloudflare's WAF.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Enumerate a domain's known DNS records

Dump Cloudflare's DNS knowledge for a target domain.

```
cloudflare-enum user@example.com Password1 example.com
```

Outcome: logs into Cloudflare with the given account credentials, adds example.com, and prints each discovered record as `A: sub.example.com -> 198.51.100.10` (and CNAME entries) with `[ STATUS ]` / `[ SUCCESS ]` progress lines — revealing hostnames and origin IPs even when the domain is proxied through Cloudflare.
