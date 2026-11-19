---
name: "catphish"
categories:
  - blackarch-social
version: "44.768d213"
homepage: "https://github.com/ring0lab/catphish"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ring0lab/catphish"
github-stars: 636
github-pushed: "2018-10-16"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# catphish

> Generates lookalike domain candidates for a target (homoglyphs, typos, punycode, ...) and reports which are available to register and how web proxies categorize them.

## What it's for

Red-team domain preparation in Ruby. From a target domain it algorithmically generates convincing variants — singular/plural forms, prepended or appended words, mirrorization, homoglyphs, dash omission, double extensions and Punycode — so you can register the ones the target forgot. Beyond fresh generation, it can find expired domains related to the target that are available again, and query web-filter categorization status: an expired domain still whitelisted by corporate proxies is a strong candidate for a phishing lure or C2 endpoint. It only produces and evaluates domain names; hosting and infrastructure are out of scope.

## Typical use cases

### Generate every lookalike variant of a domain

```
catphish -D example.com generate -A
```

Outcome: prints the generated domains across all algorithms with their registration availability; add `-V` to also show variants that are already taken.

### Find registerable expired domains

```
catphish -D example.com expired
```

Outcome: lists expired domains related to the target that are currently available to register (experimental feature).

### Pre-check web-filter categorization before buying

```
catphish -D example.com expired -c
```

Outcome: reports the categorization status of the available expired domains as security proxies see them; use `-p PROXY_TYPE` to check against a specific vendor, and pick a lookalike that proxies still trust for your engagement.

*No corpus evidence — derived from upstream documentation.*
