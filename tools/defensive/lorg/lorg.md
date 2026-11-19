---
name: "lorg"
categories:
  - blackarch-defensive
version: "98.aa4f1a3"
homepage: "https://github.com/jensvoid/lorg"
packaged-by: [blackarch]
blackarch-builddate: "2019-02-24"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "jensvoid/lorg"
github-stars: 214
github-pushed: "2019-02-22"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lorg

> Post-compromise forensic analysis of Apache access logs — finds which web application actually got attacked.

## What it's for

LORG (formerly WebForensik) performs security analysis of HTTP server log files such as Apache's access_log, aimed at finding the "needle in the haystack" — the vulnerable web application — after a site compromise. It detects attacks in request logs with three families of techniques: signature-based (PHPIDS rules), statistical (character-frequency analysis) and machine-learning (Markov chain) detection. Detected incidents are grouped into sessions and classified as hand-crafted versus automated, distinguishing a targeted human attacker from a mass scanner. Geotargeting and DNSBL lookups show whether attacks originate from a specific country, Tor, proxies or botnet address space, and attack quantification estimates success or failure from response-size anomalies, HTTP status codes or active replay of suspicious requests. It is a PHP CLI tool (needs the pcntl, simplexml, mbstring extensions) in pre-alpha state with development idle since 2019. It analyzes existing log files; it does not monitor live traffic.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Signature-scan an access log for web attacks

```
lorg -d phpids -u /var/log/httpd/access_log
```

Outcome: parses the log with the PHPIDS signature set, URL-decodes requests for readability, and prints detected incidents grouped into sessions with a hand-crafted/automated classification; use -i to select a non-default input format (common, combined, vhost, logio, cookie).

### Detect anomalous requests without signatures

```
lorg -d chars /var/log/httpd/access_log
```

Outcome: flags statistically anomalous requests by character-frequency analysis instead of signature matches, catching novel or obfuscated attacks the rules miss; -d mcshmm applies the machine-learning detector and -d all runs every detector.

### Enrich the analysis with geotargeting and DNSBL

```
lorg -d all -g -b all /var/log/httpd/access_log
```

Outcome: adds per-country geotargeting (separate GeoIP files required) and checks attacking addresses against every DNSBL category (tor, proxy, zombie, spam, dialup), showing whether sessions come from anonymizers or botnet ranges.

### Estimate which attacks actually succeeded

```
lorg -d phpids -q all /var/log/httpd/access_log
```

Outcome: quantifies detected attacks as success or failure using response status codes, response-size anomalies and active replay of suspicious requests, highlighting the sessions that actually compromised an application.

### Export findings for reporting

```
lorg -d phpids -o html /var/log/httpd/access_log report.html
```

Outcome: writes an HTML report of the analysis to report.html instead of console output; -o also accepts json, xml and csv for machine-readable post-processing.
