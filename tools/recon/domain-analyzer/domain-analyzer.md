---
name: "domain-analyzer"
categories:
  - blackarch-recon
version: "0.8.1"
homepage: "http://sourceforge.net/projects/domainanalyzer/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "eldraco/domain_analyzer"
github-stars: 1864
github-pushed: "2022-12-29"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# domain-analyzer

> Unattended domain profiler from MatesLab: gathers DNS, mail, SPF and subdomain intelligence, nmap-scans what it finds, and writes it up as an evidence directory plus a PDF report.

## What it's for

domain-analyzer takes a domain and, without hand-holding, collects its DNS footprint — name servers, mail servers, IP addresses and countries, SPF records, the most common hostnames, emails, subdomains and zone-transfer attempts — then drives nmap for host discovery, port scanning and version detection over everything found, and optionally crawls discovered web servers with its bundled crawler. Results land in a directory named after the domain, including nmap output files and an optional PDF summary. It is a Python-2-era tool (2009–2013, by Sebastian Garcia and Veronica Valeros, distributed via SourceForge): expect legacy dependencies, and treat its Google- and Robtex-dependent features as best-effort. It is the broad-but-slow option; for one specific question use a focused tool instead.

## Typical use cases

### Full unattended analysis with report

```
domain-analyzer -d example.com -o -f
```

Outcome: creates a directory `example.com/` holding all findings (including nmap output files), prints a colour-coded console summary, and `-f` additionally writes a PDF report of the results.

### Fast pass without active scanning

```
domain-analyzer -d example.com -o -a -n -w
```

Outcome: collects and stores the DNS, mail, SPF and subdomain picture but skips nmap active-host detection and port scans (`-a`), netblock reverse lookups (`-n`) and web crawling (`-w`) — much faster and quieter than a full run.

### Check your own hostname list

```
domain-analyzer -d example.com -L common-hosts.txt
```

Outcome: looks up the hostnames from your file (one per line) on the domain's DNS servers in addition to the built-in list of common names, instead of only the default 192.

### Crawl a discovered web site

```
domain-analyzer-crawler -u http://target.example.com -w -m 100 -f
```

Outcome: crawls up to 100 URLs following HTML and JavaScript redirects, writes its findings to a file (`-w`), and downloads interesting files such as documents and archives (`-f`); directory listings and mailto references are flagged along the way.

*No corpus evidence — derived from upstream documentation.*
