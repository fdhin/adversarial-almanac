---
name: "id-entify"
categories:
  - blackarch-recon
version: "34.dd064a5"
homepage: "https://github.com/BillyV4/ID-entify"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BillyV4/ID-entify"
github-stars: 116
github-pushed: "2020-05-19"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# id-entify

> Domain recon orchestrator that runs a dozen footprinting tools behind one command and filters their combined output.

## What it's for

ID-entify (BillyV4, Carlos Ramírez L.) is a shell wrapper that automates information gathering about a domain: it launches tools such as dnsrecon, dnsenum, fierce, dig, OWASP Amass, whatweb, wafw00f, nmap's http-waf NSE scripts, whois and theHarvester inside a tmux workspace, collecting emails, IP addresses, subdomains, web-technology fingerprints, firewall type and NS/MX records. It is a Python-2-era orchestrator, not a scanner itself — the external tools it drives must be installed for anything to run.

## Typical use cases

### Collect raw recon data for a domain

Run the full tool suite in the background.

```
id-entify -d example.com
```

Outcome: opens a tmux workspace running the bundled tools against the domain; raw output accumulates under `./id-domain/Raw_Data/` and the workspace closes automatically when the runs finish.

### Filter the collected data

Distill the raw dumps into readable results.

```
id-entify -g example.com
```

Outcome: filters the stored raw output into `./id-domain/Greep_Data/`, producing condensed lists of the emails, hosts, addresses and technologies found for the domain.

*No corpus evidence — derived from upstream documentation.*
