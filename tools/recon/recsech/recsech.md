---
name: "recsech"
categories:
  - blackarch-recon
version: "123.1fc298a"
homepage: "https://github.com/radenvodka/Recsech"
packaged-by: [blackarch]
blackarch-builddate: "2020-03-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "radenvodka/Recsech"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# recsech

> PHP one-shot web footprinting script: DNS, subdomains, honeypot detection, takeover checks, ports, WAF and WordPress audit in a single run.

## What it's for

Recsech is a PHP command-line tool for footprinting and reconnaissance against a target website. One invocation collects DNS information, enumerates subdomains (sorted by IP), detects honeypots in the results, checks for subdomain takeover opportunities, finds email addresses, scans ports, fingerprints technologies, detects the WAF in front of the site, and audits WordPress installations (sensitive files, version detection, version-based vulnerability lookup); it can also run requests through a proxy. It needs PHP 7+ with the curl extension. The upstream GitHub repository has been deleted (the archived README is the documentation of record), so treat the tool as unmaintained — several of its checks depend on third-party services that may have changed since.

## Typical use cases

### Run a full footprinting pass on a site

```
php Recsech.php example.com
```

Outcome: single-pass report covering DNS records, discovered subdomains (with honeypot and takeover flags), emails, open ports, detected technologies, WAF presence and — when the target runs WordPress — sensitive files and version-linked vulnerabilities.

### Debug a failing scan

```
php Recsech.php debug
```

Outcome: re-runs with diagnostic output exposed (the upstream remedy for a silently failing scan) so you can see which PHP extension or remote service is breaking the normal run.

*No corpus evidence — derived from the archived upstream README (repository deleted).*
