---
name: "aiodnsbrute"
categories:
  - blackarch-recon
version: "v0.3.2.r0.ge773a4c"
homepage: "https://github.com/blark/aiodnsbrute"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-02-02"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "blark/aiodnsbrute"
github-stars: 678
github-pushed: "2023-10-22"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aiodnsbrute

> Fast asynchronous DNS subdomain brute-forcer in Python — hundreds of thousands of resolutions per run with a single command.

## What it's for

aiodnsbrute brute-forces subdomain names against a domain using Python's asyncio, firing many concurrent DNS lookups (default 512 tasks, tunable to thousands) through non-blocking resolvers. On a well-connected host it sustains on the order of a hundred thousand resolutions in a couple of minutes. It ships with built-in wordlists, detects wildcard DNS responses so a wildcarded domain doesn't flood you with false hits, and writes results as CSV or JSON. It is a resolver-only tool: it finds names that answer in DNS and does not probe HTTP or ports. Be aware it can generate very heavy DNS traffic — point it at resolvers you're allowed to hammer.

## Typical use cases

### Quick brute force with defaults

Run against a domain with the built-in wordlist.

```
aiodnsbrute example.com
```

Outcome: performs wildcard detection first (a wildcard answer is reported and excluded), then resolves the default wordlist with up to 512 concurrent tasks, printing each hit as `subdomain -> IPs` with a progress bar and a final count of found subdomains.

### Custom wordlist and higher concurrency

Scale the scan for large targets.

```
aiodnsbrute -w wordlist.txt -t 1024 -vv example.com
```

Outcome: loads your wordlist instead of the built-in one and runs 1024 concurrent lookups with verbose logging; found names stream to the terminal. Raise `ulimit -n` if the task count outpaces the file-descriptor limit.

### Structured output for pipelines

Emit JSON to stdout only, for post-processing.

```
aiodnsbrute -f - -o json example.com | jq -r '.[] | select(.ip != null) | .domain'
```

Outcome: the JSON report goes to stdout (one record per name, with resolved IPs) while progress stays on stderr; the jq filter leaves a clean list of resolving subdomains for the next tool in the chain. `-o csv` writes `example.com.csv` instead.

### Use custom resolvers

Control (and speed up) where queries are sent.

```
aiodnsbrute -r resolvers.txt example.com
```

Outcome: queries are spread across the resolvers listed in the file (one per line, `#` comments allowed; `-r -` reads them from stdin) instead of the system defaults — faster on a VPS and keeps your traffic off any single resolver. 

### Detect CNAMEs for takeover hunting

Switch the lookup mode to gethostbyname.

```
aiodnsbrute --gethostbyname example.com
```

Outcome: each hit is resolved via gethostbyname, exposing CNAME chains that the faster default `query` mode omits — the pointer targets are what you check for dangling DNS takeover candidates.

*No corpus evidence — derived from upstream documentation.*
