---
name: "pius-pi"
categories:
  - blackarch-recon
version: "1.3.18.r0.gff1dbcb"
homepage: "https://github.com/praetorian-inc/pius"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: go
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "praetorian-inc/pius"
github-stars: 95
github-pushed: "2026-10-19"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 311
gh-activity-checked: "2026-11-09"
---

# pius-pi

> Maps an organization's external attack surface — domains, subdomains, and IP ranges (CIDRs) — from just a company name, via 26 passive discovery plugins.

## What it's for

Pius (upstream binary `pius`, packaged as `pius-pi`) answers "what does this company own on the internet?" without touching the target. Given an organization name it queries certificate transparency logs, all five Regional Internet Registries (ARIN, RIPE, APNIC, AFRINIC, LACNIC), passive DNS, WHOIS/RDAP and BGP tables through a three-phase pipeline, and reports discovered domains, subdomains and CIDR blocks with confidence scoring for ambiguous name-to-asset matches. It is passive-first by default; the only active component is the opt-in `doh-enum` plugin mode. It does not port-scan or probe discovered assets — pair it with resolvers and scanners for that.

## Typical use cases

### Discover an organization's assets from its name

Run a full passive discovery pass for a company.

```
pius-pi run --org "Example Corp"
```

Outcome: runs all applicable plugins concurrently and prints discovered domains, subdomains and CIDR ranges with confidence scores; API responses are cached locally for 24 hours, so an immediate re-run returns faster.

### Seed discovery with a known domain

Anchor the pipeline to a domain you already know belongs to the organization.

```
pius-pi run --org "Example Corp" --domain example.com
```

Outcome: same pipeline, but late-stage plugins enrich results starting from the seed domain, typically yielding tighter, higher-confidence subdomain and CIDR results.

### Enumerate IP ranges from an ASN

Resolve known network space to CIDR blocks.

```
pius-pi run --org "Example Corp" --asn AS64512 --plugins asn-bgp
```

Outcome: runs only the selected plugins and prints the CIDR blocks associated with that ASN where they match the organization.

### List available plugins

Inspect the plugin surface before selecting a subset.

```
pius-pi list
```

Outcome: prints all 26 plugins (17 domain-oriented, 9 CIDR-oriented) with descriptions; the names are the valid values for `--plugins`.

### Produce machine-readable output

Feed discovery results into other tooling.

```
pius-pi run --org "Example Corp" --output ndjson | jq 'select(.type == "cidr")'
```

Outcome: emits one JSON record per line; the jq filter keeps only CIDR discoveries. Use `--output json` instead for a single JSON document.

### Active DNS-over-HTTPS subdomain enumeration

Opt in to active subdomain guessing against one domain.

```
pius-pi run --domain example.com --mode active --plugins doh-enum
```

Outcome: performs dictionary-style resolution over DNS-over-HTTPS resolvers for the target domain and adds resolved subdomains to the results — the only mode that actively queries the target's name space.

No corpus evidence — derived from upstream documentation.
