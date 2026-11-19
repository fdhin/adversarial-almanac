---
name: "whichcdn"
categories:
  - blackarch-webapp
version: "22.5fc6ddd"
homepage: "https://github.com/Nitr4x/whichCDN"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Nitr4x/whichCDN"
github-stars: 149
github-pushed: "2017-05-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# whichcdn

> Tell whether a site is served through a CDN, and name the provider.

## What it's for

whichCDN checks whether a given website is protected by a Content Delivery Network and identifies which one — Cloudflare, Incapsula, CloudFront, Akamai, Fastly, Azure, EdgeCast, MaxCDN, Limelight and roughly a dozen more. Useful early in a web assessment, since a CDN masks the origin infrastructure and changes how scanning and WAF testing should be approached. It accepts a full URL or a bare domain and matches response data against known CDN signatures. It identifies the CDN only; it does not attempt to discover the origin behind it.

## Typical use cases

### Check a site for CDN protection

```
whichcdn example.com
```

Outcome: prints whether the site is CDN-protected and, if so, the detected provider name; a URL form (`whichcdn http://target.example.com`) works identically.

*No corpus evidence — derived from upstream documentation (README).*
