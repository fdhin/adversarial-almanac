---
name: "cloudbrute"
categories:
  - recon
homepage: "https://github.com/0xsha/CloudBrute"
github-repo: "0xsha/CloudBrute"
github-match: "exact"
origin: "awesome-azure-pentest"
recipe: "upstream"
license: MIT
github-stars: 1153
github-pushed: "2025-03-09"
gh-checked: "2026-11-13"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# cloudbrute

> Black-box hunter for a company's cloud footprint — open buckets, apps, and storage across eight providers from just a domain name.

## What it's for

CloudBrute finds a target's infrastructure, files and apps on the major cloud providers (Amazon, Google, Microsoft, DigitalOcean, Alibaba, Vultr, Linode) with no credentials, keys or cloud CLI access. It detects which provider a target uses (IPINFO API and source-code checks), then brute-forces storage and app names generated from the domain and a keyword against provider-specific URL patterns with randomized user agents and proxy support. Unauthenticated by design — the output is the bug-bounty/recon list of publicly exposed buckets and hosted apps.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Hunt storage across the detected provider

```
CloudBrute -d target.com -k target -m storage -t 80 -T 10 -w "./data/storage_small.txt"
```

Outcome: Provider auto-detection followed by concurrent storage-name brute forcing with the bundled wordlist; hits are printed live — each hit is an exposed bucket to enumerate.

### Force a specific provider and save results

```
CloudBrute -d target.com -k keyword -m storage -t 80 -T 10 -w -c amazon -o target_output.txt
```

Outcome: Skips detection and hunts only the named provider (from config.yaml's provider list), writing results to a file for later diffing between runs.

### Hunt hosted apps instead of storage

```
CloudBrute -d target.com -k target -m app
```

Outcome: Switches the generator to app-name patterns — finds internal tools and forgotten apps hosted on the provider's app platforms.

