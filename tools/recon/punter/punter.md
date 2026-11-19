---
name: "punter"
categories:
  - blackarch-recon
version: "45.97b7bed"
homepage: "https://github.com/nethunteros/punter"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "nethunteros/punter"
github-stars: 295
github-pushed: "2019-02-07"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# punter

> Python 2 era passive-footprinting helper that chains DNSDumpster, WHOIS/reverse-WHOIS, crt.sh, Have I Been Pwned and Crimeflare results into an HTML report.

## What it's for

Punter ("passive hunter") gathers a first-pass footprint of a domain without querying the target's own infrastructure: DNS records and subdomains via DNSDumpster, WHOIS plus reverse WHOIS on contact emails, additional subdomains from crt.sh, breach exposure of discovered addresses via Have I Been Pwned, and true-IP lookups behind Cloudflare via Crimeflare. Everything lands in a self-contained HTML report you open in a browser. It is Python 2 software from 2019 and unmaintained — expect breakage as the scraped services change. WHOIS/DNS lookups run on your host (not via API); Shodan and VirusTotal integration is optional and off by default until enabled with an API key in the config file.

## Typical use cases

### Run a passive footprint scan of a domain

Collect the first reconnaissance layer without touching the target.

```
punter -t example.com
```

Outcome: runs the DNSDumpster/WHOIS/crt.sh/Crimeflare/HIBP chain against the domain and writes an HTML report you can open directly — a quick starting inventory of hosts, contacts, subdomains and any Cloudflare-fronted origins.

### Refresh the Crimeflare database

Update the local copy of the Crimeflare dataset used to de-Cloudflare hosts.

```
punter -d
```

Outcome: downloads the current Crimeflare database and exits — run this periodically so subsequent scans resolve up-to-date origin IPs.

### Enable Shodan/VirusTotal with a custom config

Turn on the optional third-party enrichments for a scan.

```
punter -t example.com -c shodan.cfg
```

Outcome: reads the named config instead of the default `config.cfg`; a copy with `enable_shodan = True` under `[SERVICE]` and your key under `[API_KEYS]` adds Shodan (and likewise VirusTotal) results to the HTML report.

No corpus evidence — derived from upstream documentation.
