---
name: "cloudunflare"
categories:
  - blackarch-recon
version: "14.b91a8a7"
homepage: "https://github.com/greycatz/CloudUnflare"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-20"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "greycatz/CloudUnflare"
github-stars: 371
github-pushed: "2021-12-28"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cloudunflare

> Interactive bash pipeline that digs for a Cloudflare-protected domain's real IP via DNS, RDAP vendor lookups and historical NS/IP records.

## What it's for

CloudUnflare is a single interactive bash script: you enter a domain and it runs a fixed recon pipeline — (1) resolves CNAME and A records with dig, tagging every IP with its network operator via ARIN RDAP; (2) sweeps a bundled subdomain wordlist for any record that resolves outside Cloudflare; (3) pulls nameserver history from a configured CompleteDNS account; and (4) fetches IP history from ViewDNS.info. The historical NS and IP records are the core value: pre-Cloudflare resolutions survive in them even when current DNS is perfectly clean. CompleteDNS credentials (email|password) must be set in the `CompleteDNS_Login` variable at the top of cloudunflare.bash, and dig, curl and whois must be installed.

## Typical use cases

### Run the full pipeline

Interactive recon of one protected domain.

```
cloudunflare
```

Outcome: prompts for a domain, then prints the whole pipeline as it runs — CNAME/IP lines with vendor tags, subdomain sweep progress, CompleteDNS nameserver history and ViewDNS IP history — surfacing any address or record that bypasses Cloudflare as the real-IP lead.

*No corpus evidence — derived from upstream source (cloudunflare.bash).*
