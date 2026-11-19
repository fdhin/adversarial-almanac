---
name: "sublist3r"
categories:
  - blackarch-recon
version: "138.729d649"
homepage: "https://github.com/aboul3la/Sublist3r"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "aboul3la/Sublist3r"
github-stars: 11085
github-pushed: "2024-08-02"
github-archived: false
github-issues: 254
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sublist3r

> Classic OSINT subdomain enumerator that aggregates search engines and threat-intel sources, with an integrated bruteforce module.

## What it's for

Sublist3r by aboul3la enumerates subdomains by asking many public sources at once: search engines (Google, Yahoo, Bing, Baidu, Ask) plus Netcraft, VirusTotal, ThreatCrowd, DNSdumpster and reverse DNS. Because it merges passive sources, it is fast and sends nothing directly at the target. On top of the passive collection it can optionally brute-force DNS using the integrated subbrute module with its wordlist, and it can port-scan the hosts it finds. It is a Python tool (2 and 3) whose upstream has been inactive for years; several engines have grown unreliable since, so treat the aggregator's breadth — not any single source — as its value.

## Typical use cases

### Enumerate a domain passively

Collect subdomains from all OSINT sources at once.

```
sublist3r -d example.com
```

Outcome: queries every enabled engine and aggregator in parallel and prints the merged, deduplicated list of subdomains found.

### Keep only hosts with web ports open

Filter results down to subdomains answering on HTTP/HTTPS.

```
sublist3r -d example.com -p 80,443
```

Outcome: after enumeration, checks each discovered subdomain for open TCP ports 80 and 443 and prints only those that respond.

### Add DNS bruteforce

Supplement passive sources with a wordlist-based bruteforce.

```
sublist3r -d example.com -b -t 40
```

Outcome: enables the subbrute bruteforce module with 40 threads alongside the passive engines, catching subdomains that no public source has indexed.

### Restrict engines and save results

Use chosen engines only, writing output to a file.

```
sublist3r -d example.com -e google,yahoo,virustotal -o subs.txt
```

Outcome: enumerates using only the named engines and writes the unique subdomain list to subs.txt.

*No corpus evidence — derived from upstream documentation.*
