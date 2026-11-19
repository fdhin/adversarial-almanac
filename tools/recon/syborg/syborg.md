---
name: "syborg"
categories:
  - blackarch-recon
version: "36.5cd010b"
homepage: "https://github.com/MilindPurswani/Syborg"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "MilindPurswani/Syborg"
github-stars: 146
github-pushed: "2021-04-09"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# syborg

> Recursive DNS subdomain brute-forcer with dead-end avoidance, finding hosts that passive sources never indexed.

## What it's for

Syborg by Milind Purswani is a Python 3 subdomain enumerator that sits between passive and classic active enumeration: it constructs candidate domain names and recursively queries DNS for them, descending into a subtree only while there is evidence that names exist beneath it. Its dead-end avoidance is modeled on ettu: when a lookup returns a plain NXDOMAIN the branch is dropped, but when an ancestor of the queried name has records, enumeration continues below it — which uncovers internal and unadvertised hostnames that will never appear in certificate-transparency logs or VirusTotal-style aggregators. Reach for it when passive enumeration has plateaued and you want deeper DNS coverage against a permissive resolver; be aware it can be CPU- and query-intensive (the author suggests throttling it with cpulimit on rented servers).

## Typical use cases

### Recursive enumeration of a domain

Run the default recursive DNS crawl.

```
syborg example.com
```

Outcome: brute-forces candidate names under example.com recursively against the default resolver, pruning dead branches, and writes discovered subdomains to the default results file for the domain.

### Tune wordlist and concurrency

Scale the crawl with a better wordlist and more workers.

```
syborg example.com -w dns-words.txt -c 50
```

Outcome: enumerates using dns-words.txt instead of the bundled small default list, with concurrency raised to 50 parallel queries — the author's recommended shape for deep runs against a domain with many assets.

### Query a specific resolver with visibility

Point the crawl at a chosen nameserver and watch it work.

```
syborg example.com -d 198.51.100.10 -v
```

Outcome: all lookups go to the resolver at 198.51.100.10 (default is 8.8.8.8) and verbose mode prints enumeration progress as branches are explored and pruned.

*No corpus evidence — derived from upstream documentation (flags verified against the tool's argparse definition).*
