---
name: "lbd"
categories:
  - blackarch-recon
version: "20130719"
homepage: "http://ge.mine.nu/code/lbd"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2023-01-15"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "D3vil0p3r/lbd"
github-stars: 4
github-pushed: "2024-02-17"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lbd

> One-argument bash script that reports whether a domain sits behind DNS and/or HTTP load balancing — by counting A records and diffing HTTP headers.

## What it's for

lbd (load balancing detector, by Stefan Behte) checks a single domain for four balancing signals: DNS load balancing (the name resolving to more than one A record), differing `Server:` headers across repeated HEAD requests, `Date:` header drift between backends, and byte-level differences in the HEAD responses themselves. It sends 50 HTTP HEAD requests via netcat per check and finishes with a verdict listing which methods (DNS, HTTP[Server], HTTP[Date], HTTP[Diff]) were detected. The script self-describes as a proof-of-concept that can give false positives. It only detects that balancing exists — it does not identify the individual backends.

## Typical use cases

### Check whether a domain uses load balancing

```
lbd www.example.com
```

Outcome: prints the banner and then one FOUND/NOT FOUND verdict per method — "Checking for DNS-Loadbalancing" (listing the A records when found), "Checking for HTTP-Loadbalancing [Server]", "[Date]" and "[Diff]" — ending with a summary of the detected balancing methods. Run it before assuming a single web server is answering your requests; multiple methods detected means tools that cache or fingerprint a single response may be seeing only one member of a pool.

*No corpus evidence — derived from the packaged script itself (BlackArch `packages/lbd/lbd`, the original ge.mine.nu release).*

GitHub: third-party mirror of the original ge.mine.nu lbd release by Stefan Behte (D3vil0p3r/lbd); the mirrored script is byte-identical to the packaged copy.
