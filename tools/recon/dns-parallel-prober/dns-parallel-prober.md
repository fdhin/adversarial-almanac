---
name: "dns-parallel-prober"
categories:
  - blackarch-recon
version: "68.422db61"
homepage: "https://github.com/lorenzog/dns-parallel-prober"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "lorenzog/dns-parallel-prober"
github-stars: 109
github-pushed: "2022-10-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dns-parallel-prober

> Adaptive parallelized DNS prober that finds subdomains as fast as your resolvers allow.

## What it's for

DNS Queue (packaged as `dns-parallel-prober`) brute-forces subdomains of a
given domain with a self-tuning thread pool: you set a maximum thread count
and the prober adapts how fast it feeds those threads to the rate at which
lookups actually complete, so it stays near your configured ceiling without
manual tuning. By default it discovers and queries the domain's authoritative
name servers itself. It is an active, noisy technique — the README is blunt
that you can saturate your bandwidth and get blacklisted — so run it only
against assets you are authorized to test. A `--simulate` mode fakes the
entire run (random results, by design) so you can rehearse thread counts and
output paths safely. It ships a research-derived `subdomains.txt` wordlist.

## Typical use cases

### Dry-run a wordlist probe first

Validate thread count, wordlist and output path with zero queries sent.

```
dns-parallel-prober example.com 100 out.txt -i subdomains.txt --simulate
```

Outcome: prints the full run choreography — authoritative NS lookup, wildcard
check, progress bar over the wordlist — but resolves nothing real and writes
random results; a clean dry-run means the real invocation will behave the
same once `--simulate` is dropped.

### Probe a wordlist for real

The actual enumeration pass.

```
dns-parallel-prober example.com 100 out.txt -i subdomains.txt
```

Outcome: finds example.com's authoritative name servers, checks for wildcard
DNS, then probes every entry of subdomains.txt with up to 100 adaptively
scheduled threads and writes discovered subdomains to out.txt, finishing with
a graceful Ctrl-C-safe shutdown that drains in-flight lookups.

### Force queries through chosen resolvers

Override the auto-detected authoritative servers.

```
dns-parallel-prober example.com 100 out.txt -i subdomains.txt -n ns1.example.com -n ns2.example.com
```

Outcome: same sweep, but every lookup goes to the explicitly listed name
servers instead of the discovered authoritative ones — useful to route
queries through a resolver you control or one with better visibility.

### Brute short names without a wordlist

Let the tool generate its own candidates.

```
dns-parallel-prober example.com 100 out.txt
```

Outcome: probes generated subdomain candidates (default length 3) instead of
reading a wordlist, covering short hostnames dictionaries often miss; raise
the thread count (the FAQ suggests doubling from 100 until throughput stops
improving) when your uplink and the servers can take it.

No corpus evidence — derived from upstream documentation.
