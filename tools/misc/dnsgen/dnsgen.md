---
name: "dnsgen"
categories:
  - blackarch-misc
version: "v1.0.4.r12.g7c98e7e"
homepage: "https://github.com/AlephNullSK/dnsgen"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "AlephNullSK/dnsgen"
github-stars: 1083
github-pushed: "2025-01-03"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnsgen

> Expands known subdomains into permutation variants (dev-, -api, numbers, cloud and region patterns) for subdomain discovery.

## What it's for

dnsgen is a permutation engine for DNS reconnaissance: given domains you already know (say api.example.com), it generates plausible sibling names — word insertions and affixes (staging.api.example.com, api-dev.example.com), number manipulation (api2 → api1, api3), cloud/region/microservice naming patterns, and words from a custom wordlist. The output is meant to be resolved afterwards — typically piped into massdns — to surface registered but overlooked infrastructure during bug bounty and assessment scoping. It generates names only and performs no DNS queries itself; `-f` trades pattern coverage for speed on quick passes.

## Typical use cases

### Permute a domain list

```
dnsgen domains.txt
```

Outcome: prints every generated permutation of every input domain to stdout, ready to pipe into a resolver.

### Use a custom wordlist and write to a file

```
dnsgen -w words.txt -o results.txt domains.txt
```

Outcome: permutations are augmented with your wordlist's words (words shorter than `-l` characters, default 6, are ignored) and written to results.txt.

### Quick pass in fast mode

```
dnsgen -f domains.txt
```

Outcome: generates a reduced, high-yield pattern set — smaller and faster, useful for a first pass before committing to the full permutation run.

### Generate and resolve in one pipeline

```
cat domains.txt | dnsgen - | massdns -r resolvers.txt -t A -o J --flush 2>/dev/null
```

Outcome: permutations built from stdin (`-`) are resolved en masse by massdns; the answered names are the newly discovered subdomains.

*No corpus evidence — derived from upstream documentation.*
