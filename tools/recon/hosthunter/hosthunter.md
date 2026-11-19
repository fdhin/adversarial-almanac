---
name: "hosthunter"
categories:
  - blackarch-recon
version: "158.553f1c7"
homepage: "https://github.com/SpiderLabs/HostHunter"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "SpiderLabs/HostHunter"
github-stars: 1177
github-pushed: "2023-03-30"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hosthunter

> Maps a list of target IP addresses to their virtual hostnames using TLS certificates, HTTP headers and OSINT APIs.

## What it's for

HostHunter (SpiderLabs, by Andreas Georgiou) takes a file of IPv4/IPv6 targets and discovers which hostnames belong to each address by harvesting SSL/TLS certificates on common service ports, HTTP headers and the HackerTarget API — a quick way to enumerate the virtual-host attack surface of an IP range. It is not a port scanner or vulnerability scanner; it only produces IP-to-hostname mappings, optionally validating them with DNS resolution. Results are written as CSV or TXT.

## Typical use cases

### Hunt hostnames for a batch of target IPs

Feed a newline-delimited file of IP addresses and collect every hostname attributable to them.

```
hosthunter targets.txt
```

Outcome: scans the targets, prints the discovered mappings and writes the default report `vhosts.csv` in the working directory — `cat vhosts.csv` afterwards to review the hostnames found per address.

### Hunt a single IP and verify findings with DNS

Check one address and confirm each discovered hostname really points back to it.

```
hosthunter -t 198.51.100.10 -v
```

Outcome: hostname(s) attributed to that single IP are printed, with the verify flag attempting a DNS resolution of each name so validated mappings can be told apart from unconfirmed ones.

### Produce a plain-text hostname list

Get a clean TXT list instead of CSV when feeding results into other tools.

```
hosthunter targets.txt -f txt -o hosts.txt
```

Outcome: writes the discovered hostnames to `hosts.txt` using `--format txt --output hosts.txt`.

*No corpus evidence — derived from upstream documentation.*
