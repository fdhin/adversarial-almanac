---
name: "raccoon"
categories:
  - blackarch-recon
version: "190.44024ab"
homepage: "https://github.com/evyatarmeged/Raccoon"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "evyatarmeged/Raccoon"
github-stars: 4070
github-pushed: "2026-04-21"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# raccoon

> Offensive-security recon scanner that chains DNS, WHOIS, TLS, port/service, web-content and S3-bucket checks against one target.

## What it's for

Raccoon (PyPI package `raccoon-scanner`, command `raccoon`) runs a broad reconnaissance sweep over a single target: DNS records, DNS visual mapping, WHOIS, TLS certificate and cipher data, an Nmap port/service scan, URL fuzzing, subdomain enumeration (Google dorking, DNSDumpster, SAN discovery, bruteforce), CMS/web-server fingerprinting, robots.txt and sitemap extraction, email collection, WAF detection and vulnerable-S3-bucket scanning. Most scans run asynchronously, and every module writes its results to a separate file under a per-target output directory. It requires Nmap (and OpenSSL) to be installed. It does not exploit anything — it gathers and fingerprints; there is no multi-host/CIDR input in this version (one target per invocation).

## Typical use cases

### Run the full default sweep against a domain

```
raccoon example.com -o raccoon-out/
```

Outcome: runs all modules — DNS, WHOIS, TLS, nmap, subdomain enumeration, URL fuzzing, web data extraction — and writes per-module report files under `raccoon-out/` (one folder per target, one file per module) for review after the run.

### Route all HTTP traffic through Tor

```
raccoon --tor-routing example.com
```

Outcome: same sweep but HTTP requests exit through the local Tor SOCKS proxy on port 9050 (a running Tor service is required); total runtime increases noticeably, and destination-side logs see the Tor exit node instead of your address.

### Swap the port scan for a vulnerability-focused Nmap run

```
raccoon --vulners-nmap-scan example.com
```

Outcome: replaces the regular Nmap scan with the nmap-vulners NSE script, so the port/service results arrive annotated with known CVEs per product version instead of the plain service banner.

### Tune the fuzzing engine

```
raccoon -w /usr/share/seclists/Discovery/Web-Content/common.txt -T 50 example.com
```

Outcome: URL fuzzing and subdomain bruteforce use your wordlist instead of the built-in SecLists defaults, with 50 worker threads instead of the default 25 — faster on stable links, and the discovered paths/subdomains land in the same per-module output files.

*No corpus evidence — derived from upstream documentation.*
