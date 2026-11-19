---
name: "knock"
categories:
  - blackarch-scanner
version: "116.da43fdd"
homepage: "https://github.com/guelfoweb/knock"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-02-21"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "guelfoweb/knock"
github-stars: 4208
github-pushed: "2026-02-19"
github-archived: false
github-issues: 72
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 11
gh-activity-checked: "2026-11-09"
---

# knock

> Knockpy: async subdomain enumeration combining passive recon sources and wordlist brute-force, with TLS/HTTP validation.

## What it's for

Knock (guelfoweb's "Knockpy", now at v9) enumerates subdomains of a domain. It
combines passive reconnaissance (pluggable sources, with VirusTotal and Shodan API
support configured via `--setup`) and wordlist brute-force, then validates every
candidate: DNS resolution, HTTP/HTTPS status, TLS certificate and IP, flagging
legacy TLS 1.0/1.1 support and checking the root domain for AXFR zone transfer and
wildcard DNS. Results land in a SQLite database browsable through an interactive
report mode that can filter, export or render an HTML report. Do not confuse it with
the unrelated same-named port-knocking script `knock.py` (grongor) — this tool does
subdomain discovery only, not port knocking.

## Typical use cases

### Enumerate a domain's subdomains

```
knockpy -d example.com
```

Outcome: Runs the default enumeration against the domain and prints a table of discovered subdomains with their resolved IPs and HTTP/TLS details (status, server, certificate); results are stored in the local SQLite database for later review.

### Combine passive recon with brute-force

```
knockpy -d example.com --recon --bruteforce
```

Outcome: Passive sources are queried for known hostnames and a wordlist is brute-forced on top, maximizing coverage for domains with few indexed subdomains; use `--wordlist` to supply your own list and `--threads`/`--timeout` to tune speed.

### Check for wildcard DNS before trusting results

```
knockpy -d example.com --wildcard
```

Outcome: Queries random non-existent labels under the domain; if they resolve, wildcard DNS is in play and brute-force hits must be filtered before being reported as real subdomains — the check prints the verdict and exits.

### Browse and export previous results

```
knockpy --report list
```

Outcome: Opens the interactive report catalog over the SQLite database (show/delete/export/search, HTML export); pass a report ID or `latest` instead of `list` to jump straight into one stored scan.

*No corpus evidence — derived from upstream documentation.*
