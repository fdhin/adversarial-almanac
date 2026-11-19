---
name: "shodanhat"
categories:
  - blackarch-recon
version: "13.e5e7e68"
homepage: "https://github.com/HatBashBR/ShodanHat"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "HatBashBR/ShodanHat"
github-stars: 235
github-pushed: "2021-06-14"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shodanhat

> Shodan host intelligence from the command line — single IPs, host lists or raw search queries, with an optional nmap pass.

## What it's for

ShodanHat wraps the Shodan Python API in a small CLI: look up one host, iterate a list of hosts, or run an arbitrary Shodan search query, optionally launching nmap scans against whatever comes back. Set the API key once with --setkey (or edit constantes.py). Results can be written to a file with -o. Requires the shodan and python-nmap packages. It is a convenience wrapper — for heavy or scripted work the shodan CLI itself is more capable.

## Typical use cases

### Inspect a single host

```
python shodanhat.py -i 198.51.100.10
```

Outcome: prints what Shodan has indexed for the address — open ports, banners, location — for quick triage of an exposed host.

### Work through a list of hosts

```
python shodanhat.py -l hosts.txt -o results.txt
```

Outcome: queries every address in the file and writes the collected intelligence to the output file.

### Run a search query and nmap the results

```
python shodanhat.py -s 'apache country:"US"' --nmap
```

Outcome: executes the Shodan query and feeds the returned hosts into an nmap scan, pairing live port data with the Shodan banners.

*No corpus evidence — derived from upstream documentation.*
