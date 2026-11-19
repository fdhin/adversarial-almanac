---
name: "vulnx"
categories:
  - blackarch-webapp
version: "v1.9.r47.gbcf451d"
homepage: "https://github.com/anouarbensaad/vulnx"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "anouarbensaad/vulnx"
github-stars: 2162
github-pushed: "2023-06-07"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vulnx

> CMS intelligence and vulnerability scanner with auto shell injection, dork-based target search, DNS/subdomain gathering and port scanning.

## What it's for

VulnX (anouarbensaad) bills itself as an intelligent bot auto shell injector: it detects which CMS a target runs (WordPress, Joomla, Prestashop, Drupal, OpenCart, Magento, Lokomedia), gathers target information — subdomains, IP address, country, organization, ASN, web-server details — and, where the detected components have known vulnerabilities, can run exploits that inject a shell automatically instead of requiring manual injection one exploit at a time. It also packs a Google-dork search engine to find candidate targets by exploit name, DNS-server dumping, a quick port scanner, multi-target input, multithreading, and an interactive mode. It is an aggressive tool (recon through shell injection in one command): point it only at systems you are explicitly authorized to test.

## Typical use cases

### Full recon and exploit run against a CMS

The complete pipeline.

```
vulnx -u http://target.example.com --timeout 3 -c all -d -w --exploit
```

Outcome: identifies the CMS, enumerates CMS info (themes, plugins, users, version) with -c all, gathers web (-w) and subdomain (-d) information, then searches for known vulnerabilities and runs matching exploits (--exploit), logging results to the output directory.

### Map a domain's DNS and subdomains

Visualize the domain infrastructure.

```
vulnx -u target.example.com --dns -d --output ./dns-results
```

Outcome: dumps DNS-server and subdomain information and writes graph/map images of the domain's DNS structure (subdomains, MX and DNS data) into ./dns-results.

### Find candidate targets via dorks

Search before you scan.

```
vulnx --list-dorks
```

Outcome: prints the dork sets bundled with the tool by name and exploit name; picking one drives the dork-search mode, which finds potentially exploitable sites via search engine queries and can export the discovered targets to a logfile.

### Scan many targets from a file

Batch mode.

```
vulnx -i targets.txt --threads 20
```

Outcome: reads domains from targets.txt (one per line) and scans them with 20 concurrent workers, aggregating findings per target in the logs.

No corpus evidence — derived from upstream documentation (README usage of anouarbensaad/vulnx).
