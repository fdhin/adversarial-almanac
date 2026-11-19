---
name: "altdns"
categories:
  - blackarch-recon
version: "78.6728272"
homepage: "https://github.com/infosec-au/altdns"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-17"
build-system: python
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "infosec-au/altdns"
github-stars: 2512
github-pushed: "2025-01-09"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# altdns

> Generates altered/permutation variants of known subdomains (dev-, -staging, -prod, ...) and optionally resolves them to find hostnames wordlists miss.

## What it's for

Altdns finds subdomains that brute-force wordlists miss because they are derived from names you already know. Give it your list of discovered subdomains plus a word list of likely tokens (test, dev, staging, qa, admin, ...) and it produces a massive set of permutations and alterations — prepending, appending, and substituting tokens across the known names (e.g. `staging.api`, `api-dev`, `api2`). By default it writes this candidate list to a file for your favorite DNS brute-forcer; with `-r` it resolves the candidates itself, multi-threaded, and saves only the ones that exist. It works best with a large seed set — a couple hundred known subdomains or more — and pairs naturally with subdomain enumerators whose output becomes altdns input. It resolves names only; it does not probe services.

## Typical use cases

### Generate and resolve permutations in one pass

Feed known subdomains and a word list, and let altdns do the resolving.

```
altdns -i subdomains.txt -o data_output -w words.txt -r -s results_output.txt
```

Outcome: writes the full permutation set to `data_output`, resolves every candidate with a multi-threaded resolver, and saves the ones with live DNS records to `results_output.txt` — a ready list of previously-unknown hostnames discovered purely through naming patterns.

### Generate candidates for another bruteforcer

Keep resolution separate and hand the word-list-sized output to your tool of choice.

```
altdns -i subdomains.txt -o data_output -w words.txt
```

Outcome: `data_output` contains the complete list of mutated subdomain candidates (no `-r`, so no DNS traffic at all); point a mass resolver at this file — its size dwarfs the seed list, so this split lets you reuse existing resolver infrastructure.

### Tune resolution speed and resolver choice

Control threads and force a specific resolver.

```
altdns -i subdomains.txt -o data_output -w words.txt -r -s results_output.txt -t 200 -d 198.51.100.10
```

Outcome: the resolver runs 200 threads and queries the specified DNS server (e.g. the target's authoritative nameserver) instead of the system default, which can increase both resolution speed and the accuracy of answers for the target zone.

*No corpus evidence — derived from upstream documentation.*
