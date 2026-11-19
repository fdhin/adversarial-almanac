---
name: "dnssearch"
categories:
  - blackarch-recon
version: "20.e4ea439"
homepage: "https://github.com/evilsocket/dnssearch"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-26"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "evilsocket/dnssearch"
github-stars: 889
github-pushed: "2021-08-10"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnssearch

> Minimal concurrent subdomain bruteforcer by evilsocket: walk a wordlist against a base domain and print what resolves.

## What it's for

dnssearch does one thing: given a base domain (`-domain`) and a wordlist (`-wordlist`), it fires concurrent DNS lookups for `<word>.domain` and prints the subdomains that resolve. There is no wildcard detection, no resolver list and no passive-source mining — pair it with a filtering resolver tool such as dnsx when you need those. Reach for it when you want a tiny, fast, dependency-light bruteforcer inside a pipeline rather than a full enumeration framework.

## Typical use cases

### Bruteforce subdomains with a wordlist

```
dnssearch -domain example.com -wordlist names.txt
```

Outcome: tests each line of `names.txt` as a subdomain of example.com with 8 concurrent consumers (the default) and prints every hostname that resolves; raise parallelism with `-consumers 64`.

### Include TXT and CNAME lookups

```
dnssearch -domain example.com -wordlist names.txt -txt -cname
```

Outcome: besides the default A-record lookups, also queries TXT records for each candidate and shows CNAME results, exposing aliased hosts pointing at external services.

*No corpus evidence — derived from upstream documentation.*
