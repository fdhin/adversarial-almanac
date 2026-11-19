---
name: "gasmask"
categories:
  - blackarch-recon
version: "172.2527371"
homepage: "https://github.com/twelvesec/gasmask"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "twelvesec/gasmask"
github-stars: 1511
github-pushed: "2021-06-11"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gasmask

> All-in-one OSINT command line by Twelvesec: fan one domain across 25+ sources — whois, DNS and search engines through Shodan, Censys and Spyse — and export in four formats at once.

## What it's for

GasMasK (Python 3, GPLv3, by maldevel and others at Twelvesec) consolidates domain information gathering behind one `-d domain -i mode` interface. Modes pick the sources: `basic` (whois, DNS, reverse DNS, Bing vhosts), `nongoogle` (a dozen+ search engines and services like Netcraft, VirusTotal, DNSDumpster, crt.sh), individual sources (`google`, `bing`, `linkedin`, `twitter`, `github`, `instagram`, `dnsdump`, `shodan`, `censys`, `spyse`, ...) or comma-separated combinations. The Shodan, Censys and Spyse modes query those APIs (keys provided per-run or stored in `api_keys.txt` via `-u`/`-r`), and the Censys mode accepts full Censys search expressions with filters (`--http-server`, `--html-title`, `--tags`, ASN/country/certificate fields) plus aggregation reports. It gathers public data only; it is not a scanner.

## Typical use cases

### Basic profile of a domain

Run the cheap lookups first.

```
gasmask -d example.com -i basic
```

Outcome: prints whois record, DNS queries, reverse DNS lookups and Bing-based virtual hosts for `example.com` to the terminal.

### Hand-pick and combine modules

Select exactly the sources you want in one pass.

```
gasmask -d example.com -i whois,dns,revdns
```

Outcome: runs only the named modules against the domain — combine any of the ~25 mode keywords (e.g. `basic,yahoo,github`) to build a custom collection.

### Export results in four formats at once

Keep the findings for the report.

```
gasmask -d example.com -i basic,yahoo,github -o results/example_com
```

Outcome: writes the same result set to markdown, txt, xml and html files under the `results/` basename given with `-o`, in addition to printing them.

### Query Shodan for a domain's exposed services

Pull Shodan's view of the target.

```
gasmask -d example.com -i shodan -k SHODAN_API_KEY
```

Outcome: queries the Shodan API for the domain using the provided key and prints the services and banners Shodan has indexed for it.

### Count a Censys fingerprint

Size an exposure before pulling records.

```
gasmask -i censys -S NGINX --count
```

Outcome: runs a Censys search for hosts with an `NGINX` server header and prints only the total match count — cheap recon of a technology's footprint before committing to a full (`--Limit N`) pull.

*No corpus evidence — derived from upstream documentation.*
