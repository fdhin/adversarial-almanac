---
name: "onionscan"
categories:
  - blackarch-scanner
version: "130.da42865"
homepage: "https://github.com/s-rah/onionscan"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-27"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "s-rah/onionscan"
github-stars: 3314
github-pushed: "2024-08-09"
github-archived: false
github-issues: 86
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# onionscan

> Tor hidden-service auditor: crawls an onion site over the Tor SOCKS proxy and reports the operational-security leaks and misconfigurations that deanonymize it.

## What it's for

OnionScan probes a Tor hidden service for the mistakes that de-anonymize operators and expose them to investigators: identifying HTTP headers and server software, referenced non-onion (clearnet) addresses, other linked onion services (which build a correlation graph), EXIF data in served images, exposed directories and files (.git, backups, SSH keys, web-server config), standard ports with unexpected services, and web-app misconfigurations. It routes everything through the local Tor SOCKS proxy (default `127.0.0.1:9050`, so a running Tor is a prerequisite) and produces a ranked report of high/medium/low-risk findings; a built-in correlation lab lets researchers track many services. It is a detection tool for operators and researchers — it never attacks the service.

## Typical use cases

### Basic security report for one hidden service

```
onionscan abcdefghijklmnop.onion
```

Outcome: connects through Tor, crawls and probes the service, and prints a report grouping findings by risk level (high/medium/low) — each item names the leak found (headers, clearnet links, exposed files, EXIF, ...) so it can be fixed.

### Verbose detail for triage

```
onionscan --verbose abcdefghijklmnop.onion
```

Outcome: the same scan with full per-check progress and detail — which checks ran, what was retrieved, and why each finding fired; the form to use when working through a report item by item.

### Machine-readable output

```
onionscan --jsonReport abcdefghijklmnop.onion > report.json
```

Outcome: emits the findings as a JSON report instead of terminal text, suitable for feeding a monitoring pipeline or the correlation lab when tracking many services over time.

### Use a non-default Tor proxy

```
onionscan --torProxyAddress=127.0.0.1:9150 abcdefghijklmnop.onion
```

Outcome: routes the scan through the given SOCKS address instead of the default 9050 — e.g. Tor Browser's bundled proxy (9150) or a relay host.

### Run only the web-related checks

```
onionscan --scans web abcdefghijklmnop.onion
```

Outcome: restricts the scan to the selected scan modules (here: web), skipping the others — faster, quieter scans when you only want one aspect audited.

*No corpus evidence — derived from upstream documentation (README quick start of s-rah/onionscan).*
