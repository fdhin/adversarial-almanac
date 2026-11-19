---
name: "finalrecon"
categories:
  - blackarch-recon
version: "v1.1.8.r4.gb5db3a4"
homepage: "https://github.com/thewhiteh4t/FinalRecon"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "thewhiteh4t/FinalRecon"
github-stars: 3046
github-pushed: "2026-10-31"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 14
gh-activity-checked: "2026-11-09"
---

# finalrecon

> All-in-one automatic web reconnaissance: headers, certificate, whois, DNS, subdomains, directories, Wayback history and a fast port scan from a single Python command.

## What it's for

FinalRecon (by thewhiteh4t) gives a quick overview of a web target without chaining half a dozen separate tools. Each module runs independently or under `--full`: HTTP header analysis with security-header flagging and cookie attribute breakdown, SSL certificate details, whois with domain-expiry countdown, crawling (internal/external links, JS, CSS, images, robots, sitemaps), DNS enumeration across 40+ record types, subdomain enumeration from ~15 sources, directory enumeration with soft-404 filtering and file-extension support, Wayback URL triage (up to 50,000 URLs grouped into JS, API endpoints, parameters and sensitive extensions), and a fast top-1000 port scan. Results export to text. Several subdomain sources need optional API keys — without a key those sources are simply skipped. Designed for Linux (Kali, BlackArch); Windows and Termux are not supported.

## Typical use cases

### Run every module in one pass

Get the complete overview of a target with a single command.

```
finalrecon --full --url https://example.com
```

Outcome: runs all modules in sequence (headers, sslinfo, whois, crawl, dns, sub, dir, wayback, port scan) and prints each section to the terminal; results are also written to an export folder under `~/.local/share/finalrecon` by default.

### Quick triage without heavy scanning

Check the cheap, high-signal facts about a site first.

```
finalrecon --headers --sslinfo --whois --url https://example.com
```

Outcome: prints categorized response headers (missing security headers flagged), the SSL certificate chain details, and the whois record with expiry countdown and DNSSEC status — enough to plan the deeper recon.

### Map DNS, subdomains and historical URLs

Enumerate the name space and archived attack surface of a domain.

```
finalrecon --dns --sub --wayback --url https://example.com
```

Outcome: prints DNS records grouped by category (security records highlighted), a deduplicated subdomain list merged from all configured sources, and up to 50,000 Wayback URLs triaged into JS files, API endpoints, juicy parameters and sensitive extensions.

### Directory brute force with extensions

Look for hidden paths and backed-up files.

```
finalrecon --dir --url https://example.com -e txt,php -dt 50
```

Outcome: probes the built-in wordlist (optionally with each listed extension appended) with soft-404 filtering and phantom-URL false-positive reduction, printing discovered paths; `-w` swaps in a custom wordlist.

### Save an API key for subdomain sources

Preload one of the optional data-source keys.

```
finalrecon -k 'shodan@SHODAN_API_KEY'
```

Outcome: validates and stores the key in `$HOME/.config/finalrecon/keys.json`; the matching subdomain source is used on the next `--sub` run (valid names: bevigil, chaos, github, hunter, leakix, netlas, shodan, virustotal, zoomeye — alternatively export `FR_SHODAN_KEY`-style environment variables).

*No corpus evidence — derived from upstream documentation.*
