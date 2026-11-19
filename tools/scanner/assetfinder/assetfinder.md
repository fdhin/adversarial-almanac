---
name: "assetfinder"
categories:
  - blackarch-scanner
version: "v0.1.1.r0.g4e95d87"
homepage: "https://github.com/tomnomnom/assetfinder"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-02-14"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "tomnomnom/assetfinder"
github-stars: 3700
github-pushed: "2024-06-07"
github-archived: false
github-issues: 43
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# assetfinder

> tomnomnom's fast passive subdomain finder: one domain in, related domains and subdomains out.

## What it's for

assetfinder (tomnomnom, Go) queries passive sources — crt.sh, certspotter, hackertarget,
threatcrowd, the Wayback Machine and dns.bufferover.run, plus VirusTotal, Facebook CT and
findsubdomains when their API keys are set — and prints every domain and subdomain
potentially related to the given root, without sending any traffic to the target's own
infrastructure. Output is a plain newline-separated list (it does not resolve anything by
design), meant to be piped into resolution or HTTP-probing tools. The single flag
`--subs-only` restricts output to subdomains of the requested domain instead of every
related root.

## Typical use cases

### Find potentially related domains

The basic passive discovery run.

```
assetfinder example.com
```

Outcome: prints all discovered domains and subdomains, one per line — including sibling
and related roots, so filter the list to your scope as needed.

### Subdomains only, deduplicated for a probing pipeline

Prepare clean input for the next tool.

```
assetfinder --subs-only example.com | sort -u > subs.txt
```

Outcome: writes only names under `example.com` to `subs.txt`, ready to feed httpx, dnsx or
a resolution loop.

### Merge in an API-key source

Add VirusTotal's passive DNS to the results.

```
VT_API_KEY=yourkey assetfinder --subs-only example.com
```

Outcome: folds VirusTotal's passive DNS into the output; the Facebook certificate source
enables the same way via `FB_APP_ID` and `FB_APP_SECRET`.

*No corpus evidence — derived from upstream documentation (README usage and source list).*
