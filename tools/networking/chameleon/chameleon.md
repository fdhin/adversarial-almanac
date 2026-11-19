---
name: "chameleon"
categories:
  - blackarch-networking
version: "29.4414287"
homepage: "https://github.com/mdsecactivebreach/Chameleon"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "mdsecactivebreach/Chameleon"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# chameleon

> Check and re-categorise how web proxies (Blue Coat, McAfee TrustedSource, IBM X-Force) classify a domain.

## What it's for

An MDSec ActiveBreach red-team tool (by Dominic Chell) that manipulates proxy categorisation: web filters allow and block by category, so attack infrastructure filed under an innocuous-but-permitted category blends into corporate traffic. Chameleon queries the category currently assigned to a domain by Blue Coat WebPulse, McAfee TrustedSource and IBM X-Force, and submits re-categorisation requests; further proxies can be added as modules under `modules/`. The upstream repository has been deleted, so this note is grounded in the archived project page. Heed the author's caution: for Blue Coat, submit without checking first, since checking a never-categorised domain can leave it uncategorised.

## Typical use cases

### Check a domain's current proxy categories

See how the major web filters classify a domain.

```
chameleon --proxy a --check --domain example.com
```

Outcome: prints the category each supported proxy currently assigns to the domain — `--proxy a` checks all three; use `b`, `m` or `i` to target Blue Coat, McAfee or IBM X-Force alone.

### Submit a domain for re-categorisation

Move infrastructure into a permitted category.

```
chameleon --proxy m --submit --domain example.com
```

Outcome: submits the domain to McAfee TrustedSource for categorisation under the finance category (this build targets finance; there is no category flag) — the project notes categorisation can take up to six hours to apply.

*No corpus evidence — derived from upstream documentation.*
