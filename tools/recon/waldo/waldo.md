---
name: "waldo"
categories:
  - blackarch-recon
version: "29.ee4f960"
homepage: "https://github.com/red-team-labs/waldo"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "red-team-labs/waldo"
github-stars: 54
github-pushed: "2016-09-10"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# waldo

> Lightweight multithreaded Python brute-forcer for hidden web directories and undiscovered subdomains.

## What it's for

Waldo (red-team-labs) answers two early-recon questions with one tool: which subdomains exist under a domain, and which directories exist on a web server. It runs a wordlist against the target in one of two modes — `s` for subdomains (DNS resolution) and `d` for directories (HTTP requests) — with a small default thread pool that can be raised for faster sweeps. It is purely wordlist-driven: it does not enumerate via certificate transparency or DNS zone transfers, and it does no vulnerability checking — every hit still needs manual triage. Output always goes to a log file (default `waldo-output.txt`) rather than being designed for piping.

## Typical use cases

### Enumerate subdomains of a domain

Find which wordlist prefixes resolve as hostnames under the target.

```
python waldo.py -m s -d example.com
```

Outcome: brute-forces subdomains of `example.com` with the built-in wordlist and DNS requests; confirmed hostnames are printed and appended to `waldo-output.txt`.

### Brute-force hidden directories on a web root

Locate undiscovered paths worth manual inspection.

```
python waldo.py -m d -d example.com -t 15 -l hits.txt
```

Outcome: requests each wordlist entry as a directory under `example.com` using 15 threads and logs discovered paths to `hits.txt` instead of the default `waldo-output.txt`.

*No corpus evidence — derived from upstream documentation.*
